---
title : "Create ECS Cluster"
date :  "`r Sys.Date()`" 
weight : 4 
chapter : false
pre : " <b> 2.2.4 </b> "
---

### Create ECS Cluster

Create **ecs.tf** with the configuration below:

```tf
resource "aws_ecs_cluster" "main" {
  name  = var.ecs_cluster_name

  tags = {
    Name     = var.ecs_cluster_name
  }
}
```

The name of the ECS Cluster is the variable `var.ecs_cluster_name`. Follow this link for checking all defined variables.

### Create Task Definition

Next, we will create Task Definition. In **ecs.tf**, adding the configuration as below:

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

Our Task Definition contains a container, which is launched from a Docker Image from Docker Hub. This container exposes port `var.container_port` (3000). When a container running on an EC2 Cluster, it will bind the port of the container to the one of ports in range 49153-65535 or 32768-61000 on the EC2 Cluster. And the most important is we can allocate resources for the container:

- 0.5 vCPU for the task (This task contains only one container)
- 500 MiB of memory for this task (This task contains only one container container)

So, we solved the problem of allocating computing resources.

### Create ECS Service

Next, we will crete ECS Service (Service will be responsible for launching task). But first, we need to create IAM Role for Service to allow them interacting with EC2 Cluster and Load Balancer. In **iamrole.tf**, adding the configuration as below:

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

Finally, we will create ECS Service. In **ecs.tf**, adding the configuration as below:

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

With the above configuration, we have:

- ECS Service belonging to the defined ECS Cluster
- IAM Role for ECS Service
- Task Definition in ECS Service
- Task (Container(s)) will be launched into EC2 Cluster and registered into the Target Group. So, Load Balancer can forward traffic to them.
- Task will be distributed across Availability Zones to ensure the High Availability
- In case of increasing the number of desired task, ECS Service guarantees using all available space on the EC2 Cluster.


Now, we have a completed AWS Infrastructure.

![ConnectPrivate](/FCJ2024-Workshop1/images/arc-log.png) 