---
title : "Tạo Auto Scaling Group"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 2.2.3 </b> "
---

Bây giờ, ta sẽ khởi tạo Auto Scaling Group cho các EC2 Cluster. Tạo file **asg.tf** với cấu hình bên dưới:

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

Với cấu hình trên, Auto Scaling Group (ASG) sẽ launch các EC2 Cluster (dựa trên AMI mà ta vừa tạo trước đó) vào trong các Private Subnet 1 và Private Subnet 2. ASG sẽ luôn đảm bảo có ít nhất 2 EC2 chạy cùng lúc. Trường hợp cần phải scale up, chúng ta sẽ có nhiều nhất 4 EC2 Cluster chạy cùng lúc. Tuy nhiên, ta cần cấu hình để đảm bảo EC2 Cluster sẽ được scale up. Tại file **asg.tf**, thêm vào cấu hình như bên dưới:

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

Với cấu hình trên, nếu CPU Utilization vượt ngưỡng 50% trong vòng 3 phút, ASG sẽ tiến hành scale up các EC2 Cluster.