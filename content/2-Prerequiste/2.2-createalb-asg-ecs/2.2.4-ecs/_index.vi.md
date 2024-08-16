---
title : "Tạo ECS Cluster"
date :  "`r Sys.Date()`" 
weight : 4 
chapter : false
pre : " <b> 2.2.4 </b> "
---

### Khởi tạo ECS cơ bản

Tạo file **ecs.tf** với cấu hình bên dưới:

```tf
resource "aws_ecs_cluster" "main" {
  name  = var.ecs_cluster_name

  tags = {
    Name     = var.ecs_cluster_name
  }
}
```

Lúc này, ta đã khai báo ECS Cluster với tên là `var.ecs_cluster_name`. Bạn đọc tham khảo source code cho các variable tại [đây](https://github.com/heyyytamvo/AWS-DevOps/blob/main/ECS/AWS-FCJ-WORKSHOP/terraform.tfvars)

### Khởi tạo Task Definition

Tiếp đến, ta sẽ tạo Task Definition. Tại file **ecs.tf**, bổ sung cấu hình bên dưới:

```tf
resource "aws_ecs_task_definition" "my_ECS_TD" {
  family             = "ECS_TaskDefinition"

  container_definitions = jsonencode([
    {
      name         = var.container_name
      image        = var.image_name
      cpu          = 500
      memory       = 500
      essential    = true
      portMappings = [
        {
          containerPort = var.container_port
          hostPort      = 0
          protocol      = "tcp"
        }
      ]
    }
  ])
}
```

Task Definition của chúng ta sẽ bao gồm 1 container và nó được launch từ một docker image từ docker hub. Container này expose port `var.container_port` (3000),
khi container chạy trên EC2 Cluster, port 3000 của container sẽ được map đến range port 49153-65535 hoặc 32768-61000 của EC2 Cluster. Và quan trọng nhất, ta sẽ cấp phát:
- 0.5 vCPU cho task này (Task này chỉ dùng 1 container)
- 500 MiB memory cho task này (Task này chỉ dùng 1 container)

Như vậy, ta đã giải quyết được bài toán cấp phát tài nguyên cho Container.

### Khởi tạo ECS Service

Tiếp đến, ta sẽ tạo ECS Service (Service sẽ chịu trách nhiệm launch các Task mà ta vừa định nghĩa). Nhưng trước tiên, ta sẽ tạo IAM Role cho Service để cho phép Service tương tác với EC2 Cluster và Load Balancer. Tại file **iamrole.tf**, thêm vào cấu hình bên dưới:

```tf
# iamrole.tf
data "aws_iam_policy_document" "ecs_service_policy" {
  statement {
    actions = ["sts:AssumeRole"]
    effect  = "Allow"

    principals {
      type        = "Service"
      identifiers = ["ecs.amazonaws.com",]
    }
  }
}


resource "aws_iam_role" "ecs_service_role" {
  name               = "ECS_ServiceRole"
  assume_role_policy = data.aws_iam_policy_document.ecs_service_policy.json
}

resource "aws_iam_role_policy" "ecs_service_role_policy" {
  name   = "ECS_ServiceRolePolicy"
  policy = data.aws_iam_policy_document.ecs_service_role_policy.json
  role   = aws_iam_role.ecs_service_role.id
}

data "aws_iam_policy_document" "ecs_service_role_policy" {
  statement {
    effect  = "Allow"
    actions = [
      "ec2:AuthorizeSecurityGroupIngress",
      "ec2:Describe*",
      "elasticloadbalancing:DeregisterInstancesFromLoadBalancer",
      "elasticloadbalancing:DeregisterTargets",
      "elasticloadbalancing:Describe*",
      "elasticloadbalancing:RegisterInstancesWithLoadBalancer",
      "elasticloadbalancing:RegisterTargets",
      "ec2:DescribeTags",
      "logs:CreateLogGroup",
      "logs:CreateLogStream",
      "logs:DescribeLogStreams",
      "logs:PutSubscriptionFilter",
      "logs:PutLogEvents"
    ]
    resources = ["*"]
  }
}
```

Cuối cùng, ta sẽ tạo ECS Service. Tại file **ecs.tf**, thêm vào cấu hình bên dưới:

```tf
resource "aws_ecs_service" "service" {
  name                               = "ECS_Service"
  iam_role                           = aws_iam_role.ecs_service_role.arn
  cluster                            = aws_ecs_cluster.main.id
  task_definition                     = aws_ecs_task_definition.my_ECS_TD.arn
  desired_count                      = var.ecs_task_desired_count

  load_balancer {
    target_group_arn = aws_alb_target_group.alb_target_group.arn
    container_name   = var.container_name
    container_port   = var.container_port
  }

  ## Spread tasks evenly accross all Availability Zones for High Availability
  ordered_placement_strategy {
    type  = "spread"
    field = "attribute:ecs.availability-zone"
  }
  
  ## Make use of all available space on the Container Instances
  ordered_placement_strategy {
    type  = "binpack"
    field = "memory"
  }

  # Do not update desired count again to avoid a reset to this number on every deploymengit t
  lifecycle {
    ignore_changes = [desired_count]
  }
}
```

Với cấu hình trên, ta sẽ có:
- ECS Service thuộc ECS Cluster mà ta vừa tạo
- ECS Service có IAM Role mà ta vừa tạo ở trên
- ECS Service sẽ có Task Definition ta vừa tạo ở trên
- ECS Service sẽ có desired task count, ví dụ 2
- Các launched task sẽ được khởi chạy trong Target Group của Load Balancer. Từ đó, Load Balancer sẽ forward traffic đến các task này (mỗi task tương ứng với một container tuỳ thuộc vào việc ta cấu hình).
- Các task sẽ được phân bố đều ở các Availability Zone để đảm bảo tính High Availability
- Trường hợp tăng số lượng task (hay container), ECS Service sẽ đảm bảo sử dụng triệt để tài nguyên của một EC2 Cluster.

Như vậy, ta đã có hoàn chỉnh hạ tầng cần phải triển khai.

![ConnectPrivate](/images/arc-log.png) 