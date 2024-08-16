---
title : "Tạo Template cho các EC2 trong ECS Cluster"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2.2.2 </b> "
---

### Tạo Security Group
Chúng ta cần tạo các template cho các EC2 trong ECS Cluster. Các container của chúng ta sẽ được chạy trong các EC2 này (Ta sẽ gọi chúng là EC2 Cluster). Trước tiên, ta sẽ cấu hình Security Group cho các EC2 Cluster trên.

Tạo file **ecs_ec2.tf** với cấu hình bên dưới:

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

Với Security Group vừa tạo, EC2 Cluster của chúng ta sẽ chấp nhận:

- Tất cả traffic từ Load Balancer đến range port 49153-65535 của EC2 Cluster
- Tất cả traffic từ Load Balancer đến range port 32768-61000 của EC2 Cluster
- SSH Protocol đến từ Bastion Host.
- Tất cả các outbound traffic.

Bạn đọc có thể thắc mắc tại sao chúng ta lại có range port như trên. Câu trả lời chính là các container của chúng ta sau này sẽ “lắng nghe” traffic từ các range port trên. Chúng được gọi là **ephemeral port**. Ví dụ: Trong Cluster của ta sẽ có 2 EC2 (1) và (2), trong đó:

- EC2 (1) sẽ có 3 container giống nhau, và chúng “lắng nghe” traffic theo thứ tự port: 49153, 49154, và 49155
- EC2 (2) sẽ có 3 container giống nhau, và chúng “lắng nghe” traffic theo thứ tự port: 32769,  32770, và 32771.

### Tạo IAM Role cho EC2 Cluster

Ta cần cấu hình IAM Role cho các EC2 Cluster này để có thể tương tác với ECS Cluster. Tạo file **iamrole.tf** với cấu hình bên dưới.

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


### Cấu hình AMI cho EC2 Cluster
Tiếp theo, tạo file **user_data.sh** như bên dưới. EC2 Cluster của chúng ta sẽ chạy tất cả các lệnh trong này trong lúc được khởi tạo. Để các EC2 này nằm trong ECS Cluster, ta cần cấu hình tại file **/etc/ecs/ecs.config** của chúng.

```sh
#!/bin/bash
echo ECS_CLUSTER='${ecs_cluster_name}' >> /etc/ecs/ecs.config
```

Và cuối cùng, chúng ta sẽ cấu hình AMI cho EC2. Tại file **ecs_ec2.tf**, thêm vào cấu hình bên dưới:

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

Lúc này, variable `aws_ecs_cluster.main.name` sẽ là tên của ECS cluster mà ta sẽ khởi tạo trong phần tiếp theo. 
