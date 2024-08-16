---
title : "Create Load Balancer and Target Group"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 2.2.1 </b> "
---


### Create Load Balancer

First, we create Security Group for our Load Balancer. Create **alb.tf** with the configuration below:


```tf
# Security Group for ALB
resource "aws_security_group" "ALB_SG" {
  name        = "ALB SG"
  description = "Allow HTTP inbound and all outbound traffic"
  vpc_id      = aws_vpc.main.id

  ingress = [
  {
    from_port        = 80
    to_port          = 80
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
    description      = "Allow inbound traffic on port 80"
    ipv6_cidr_blocks = []
    prefix_list_ids   = []
    security_groups   = []
    self             = false
  },
]

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "ALB SG"
  }
}
```

Now, we created Security Group for Load Balancer. This Security Group allows all traffic from port 80 of the Load Balancer.

Next, we will create the Load Balancer. In file **alb.tf**, adding these configuration below: 

```tf
resource "aws_alb" "alb_vlsm" {
  name               = "ALB-ECS"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.ALB_SG.id]
  subnets            = [aws_subnet.public_subnet_1.id, aws_subnet.public_subnet_2.id]

  # enable_deletion_protection = false


  tags = {
    Environment = "production"
  }
}
```

### Create Target Group

Load Balancer will perform check health on all targets in the Target Group and traffic will be only fowarded to "*healthy*" target.  


{{% notice info %}}
In case **all** targets are considered *unhealthy*, Load Balancer still forwards traffic to those targets. Please note this!
{{% /notice %}}

In **alb.sg**, adding the additional configuration as below:

```tf
## Target Group for our service
resource "aws_alb_target_group" "alb_target_group" {
  name                 = "TargetGroupForService"
  port                 = "80"
  protocol             = "HTTP"
  vpc_id               = aws_vpc.main.id
  deregistration_delay = 120

  health_check {
    healthy_threshold   = "2"
    unhealthy_threshold = "2"
    interval            = "60"
    path                = "/"
    port                = "traffic-port"
    protocol            = "HTTP"
    timeout             = "30"
  }
}
```

The Load Balancer will send HTTP requests to all targets every 60 seconds. After 2 consecutive successful responses (status 200 by default, depending on how it's configured), the target will be considered *Healthy*. In contrast, the target is marked as '*Unhealthy*' if it fails to respond successfully for 2 consecutive checks.


### Configure Load Balancer Listener and Listener Rule


We need to configure Listener for Load Balancer. Listener is the process of listening traffic on a configured port. For example, Load Balancer will listen on port 80 (for HTTP request) and port 443 (for HTTPS request) 

In **alb.tf**, adding the configuration as below:

```tf
resource "aws_alb_listener" "alb_listener_http" {
  load_balancer_arn = aws_alb.alb_vlsm.arn
  port              = "80"
  protocol          = "HTTP"

  default_action {
    type = "fixed-response"

    fixed_response {
      content_type = "text/plain"
      message_body = "Access denied"
      status_code  = "403"
    }
  }
}

resource "aws_alb_listener_rule" "alb_listener_http" {
  listener_arn = aws_alb_listener.alb_listener_http.arn

  action {
    type             = "forward"
    target_group_arn = aws_alb_target_group.alb_target_group.arn
  }

  condition {
    path_pattern {
      values = ["/*"]
    }
  }
}
```
