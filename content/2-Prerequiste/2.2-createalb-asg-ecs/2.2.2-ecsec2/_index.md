---
title : "Create Template for EC2 belonging to ECS Cluster"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2.2.2 </b> "
---

### Create Security Group

We need to create template for all EC2 belonging to the ECS Cluster. All container will run on these EC2 Instance. From now, let's call them EC2 Cluster. But first, we will create Security Group for EC2 Cluster
Create **ecs_ec2.tf** with the configuration below:

```tf
# ecs_ec2.tf
resource "aws_security_group" "ECS_EC2_SG"{
  name        = "ECS_EC2_SG"
  description = "Allow traffic from ALB_SG and 22 from Bastion"
  vpc_id      = aws_vpc.main.id

  ingress = [
  {
    from_port        = 49153
    to_port          = 65535
    protocol         = "tcp"
    cidr_blocks      = []
    description      = "Allow all traffic coming from port 49153-65535 from ALB"
    ipv6_cidr_blocks = []
    prefix_list_ids   = []
    security_groups   = [aws_security_group.ALB_SG.id]
    self             = false
  },

  {
    from_port        = 32768
    to_port          = 61000
    protocol         = "tcp"
    cidr_blocks      = []
    description      = "Allow all traffic coming from port 32768-61000 from ALB"
    ipv6_cidr_blocks = []
    prefix_list_ids   = []
    security_groups   = [aws_security_group.ALB_SG.id]
    self             = false
  },

  {
    from_port        = 22
    to_port          = 22
    protocol         = "tcp"
    cidr_blocks      = []
    description      = "Allow SSH traffic from Bastion host"
    ipv6_cidr_blocks = []
    prefix_list_ids   = []
    security_groups  = [aws_security_group.Bastion_SG.id]
    self             = false
  }
]

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "ECS EC2 SG"
  }
}
```

With the above Security Group, we have this configuration:

- EC2 Cluster accepts all inbound traffic (from Load Balancer) on range ports 49153-65535
- EC2 Cluster accepts all inbound traffic (from Load Balancer) on range ports 32768-61000
- EC2 Cluster accepts SSH Protocol from Bastion Host
- EC2 Cluster allows all outbound traffic

You may wonder why we have ranges port 49153-65535 and 32768-61000. The answer is that the container will listen to these range ports after it is successfully launched on the EC2 Cluster. There is a terminology for these ports: ephemeral port.

For example, given an ECS Cluster containing 2 EC2 (1) and (2), then:

- On EC2 (1): there are 3 running containers, which listen on ports 49153, 49154, and 49155, respectively.
- On EC2 (2): there are 3 running containers, which listen on ports 32769, 32770, and 32771, respectively.


### Create IAM Role for EC2 Cluster

We need to create IAM Role for EC2 Cluster to allow them communicating with the ECS Cluster. Create **iamrole.tf** with the configuration as below:


```tf
data "aws_iam_policy_document" "ec2_instance_role_policy" {
  statement {
    actions = ["sts:AssumeRole"]
    effect  = "Allow"

    principals {
      type        = "Service"
      identifiers = [
        "ec2.amazonaws.com",
        "ecs.amazonaws.com"
      ]
    }
  }
}

resource "aws_iam_role_policy_attachment" "ec2_instance_role_policy" {
  role       = aws_iam_role.ec2_instance_role.name
  policy_arn = "arn:aws:iam::aws:policy/service-role/AmazonEC2ContainerServiceforEC2Role"
}

resource "aws_iam_role" "ec2_instance_role" {
  name               = "ECS_EC2_InstanceRole"
  assume_role_policy = data.aws_iam_policy_document.ec2_instance_role_policy.json
}

resource "aws_iam_instance_profile" "ec2_instance_role_profile" {
  name  = "EC2_InstanceRoleProfile"
  role  = aws_iam_role.ec2_instance_role.id
}
```


### Create AMI for EC2 Cluster

Next, we will create **user_data.sh** as below. Our EC2 Cluster need to execute the command line below during the process of initialization. We need to configure file **/etc/ecs/ecs.config** to register these instance into the ECS Cluster.

```sh
#!/bin/bash
echo ECS_CLUSTER='${ecs_cluster_name}' >> /etc/ecs/ecs.config
```

Create **ecs_ec2.tf** as below:

```tf
data "aws_ami" "amazon_linux_2" {
    most_recent = true

    filter {
        name   = "virtualization-type"
        values = ["hvm"]
    }

    filter {
        name   = "owner-alias"
        values = ["amazon"]
    }

    filter {
        name   = "name"
        values = ["amzn2-ami-ecs-hvm-*-x86_64-ebs"]
    }

    owners = ["amazon"]
}

## Launch template for all EC2 instances that are part of the ECS cluster
resource "aws_launch_template" "ecs_launch_template" {
    name                   = "ECS_EC2_LaunchTemplate"
    image_id               = data.aws_ami.amazon_linux_2.id
    instance_type          = var.ec2_instance_type
    key_name               = aws_key_pair.EC2key.key_name
    user_data              = base64encode(templatefile("${path.module}/user_data.sh", {ecs_cluster_name = aws_ecs_cluster.main.name})
)
    vpc_security_group_ids = [aws_security_group.ECS_EC2_SG.id]

    iam_instance_profile {
        arn = aws_iam_instance_profile.ec2_instance_role_profile.arn
    }

    monitoring {
        enabled = true
    }
}
```

Now, `aws_ecs_cluster.main.name` is the name of our ECS Cluster, which will be specified in the next section.