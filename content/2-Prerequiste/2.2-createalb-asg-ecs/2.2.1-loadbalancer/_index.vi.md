---
title : "Tạo Load Balancer và Target Group"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 2.2.1 </b> "
---


### Tạo Load Balancer

Chúng ta sẽ bắt đầu tạo Load Balancer bằng việc khởi tạo Security Group cho Load Balancer. Tạo file **alb.tf** với cấu hình bên dưới:

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
Bây giờ chúng ta đã cấu hình cho Security Group của Load Balancer. Security Group đơn giản này sẽ cho phép tất cả inbound traffic đến Load Balancer qua port 80.


Tiếp đến, ta sẽ tạo Load Balancer. Tại file **alb.tf**, thêm vào cấu hình như bên dưới:

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

### Tạo Target Group

Target Group chính là nơi mà Load Balancer sẽ forward traffic từ internet vào. Thêm nữa, Load Balancer sẽ kiểm tra độ “*healthy*” của các target trong Target Group. Load Balancer sẽ chỉ forward traffic vào các *healthy target*. 

{{% notice info %}}
Trường hợp tất cả các Target là *unhealthy*, Load Balancer vẫn sẽ forward traffic vào chúng. Đây chính là trường hợp đáng lưu ý.
{{% /notice %}}

Tại file **alb.sg**, thêm vào cấu hình như bên dưới:

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

Cứ 60 giây, Load Balancer sẽ gởi HTTP request đến các target. Sau 2 lần check liên tiếp,  nếu mỗi response trả về là **Status 200** (Phần này ta sẽ không cấu hình mà để giá trị default 200), target sẽ được xem là *healthy*. Ngược lại, target sẽ được xem là *unhealthy*.

### Cấu hình Load Balancer Listener và Listener Rule

Chúng ta cần cấu hình Listener cho Load Balancer, nói đơn giản, Listener là quá trình mà Load Balancer “*lắng nghe*” traffic (Ví dụ, cấu hình Load Balancer “*lắng nghe*” traffic đến từ port 80 hoặc 443).

Tại file **alb.tf**, thêm vào cấu hình như bên dưới:

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

Với cấu hình trên Load Balancer sẽ “lắng nghe” traffic từ port 80. Tiếp đến, Load Balancer sẽ thực hiện health check trên target group và sẵn sàng forward traffic vào chúng.
