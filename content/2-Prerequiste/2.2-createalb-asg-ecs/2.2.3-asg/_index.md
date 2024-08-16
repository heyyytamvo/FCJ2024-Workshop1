---
title : "Create Auto Scaling Group"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 2.2.3 </b> "
---

Now, we will create Auto Scaling Group for EC2 Cluster. Create **asg.tf** with the configuration below:

```tf
resource "aws_autoscaling_group" "ecs_autoscaling_group" {
  name                  = "ECS_ASG"
  max_size              = 4
  min_size              = 2
  desired_capacity      = 2
  vpc_zone_identifier   = [aws_subnet.private_subnet_1.id, aws_subnet.private_subnet_2.id]
  health_check_type     = "EC2"
  protect_from_scale_in = true

  enabled_metrics = [
    "GroupMinSize",
    "GroupMaxSize",
    "GroupDesiredCapacity",
    "GroupInServiceInstances",
    "GroupPendingInstances",
    "GroupStandbyInstances",
    "GroupTerminatingInstances",
    "GroupTotalInstances"
  ]

  launch_template {
    id      = aws_launch_template.ecs_launch_template.id
    version = "$Latest"
  }

  instance_refresh {
    strategy = "Rolling"
  }

  tag {
    key                 = "Name"
    value               = "ASG For ECS"
    propagate_at_launch = true
  }
}
```

With the configuration above, Auto Scaling Group (ASG) will launch EC2 Cluster into private subnet 1 and 2 based on the AMI, which is defined in the previous section. ASG guarantees that at least there are 2 EC2 running at the same time. In case of scaling up, there will be 4 EC2 Instances in the ECS Cluster. However, we need to configure that. In **asg.tf**, adding the configuration as below:

```tf
resource "aws_autoscaling_policy" "asg_policy_ecs_autoscaling_group" {
  autoscaling_group_name = aws_autoscaling_group.ecs_autoscaling_group.name
  name                   = "Custom Policy"
  policy_type            = "TargetTrackingScaling"
  
  target_tracking_configuration {
    predefined_metric_specification {
      predefined_metric_type = "ASGAverageCPUUtilization"
    }

    target_value = 50.0
  }
}
```

In 3 minutes, if CPU Utilization reachs 50%, ASG will launch another EC2 Instance to reduce the heavy workload.