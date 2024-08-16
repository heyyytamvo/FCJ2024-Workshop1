---
title : "Tạo Subnet"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2.1.2 </b> "
---

#### Tạo Public và Private subnet

Tạo file **subnet.tf** với cấu hình như bên dưới:

```tf
# subnet.tf
resource "aws_subnet" "public_subnet_1" {
  vpc_id              = aws_vpc.main.id
  cidr_block          = var.vpc_public_subnets[0]
  availability_zone   = var.vpc_azs[0]

  tags = {
    Name = "Public Subnet 1"
  }
}

resource "aws_subnet" "public_subnet_2" {
  vpc_id              = aws_vpc.main.id
  cidr_block          = var.vpc_public_subnets[1]
  availability_zone   = var.vpc_azs[1]

  tags = {
    Name = "Public Subnet 2"
  }
}

resource "aws_subnet" "private_subnet_1" {
  vpc_id              = aws_vpc.main.id
  cidr_block          = var.vpc_private_subnets[0]
  availability_zone   = var.vpc_azs[0]

  tags = {
    Name = "Private Subnet 1"
  }
}

resource "aws_subnet" "private_subnet_2" {
  vpc_id              = aws_vpc.main.id
  cidr_block          = var.vpc_private_subnets[1]
  availability_zone   = var.vpc_azs[1]

  tags = {
    Name = "Private Subnet 2"
  }
}
```

Chúng ta sẽ có 4 subnet tổng cộng:

- Public Subnet 1 (CIDR Block: **10.0.1.0/24**)
- Private Subnet 1 (CIDR Block: **10.0.3.0/24**)
- Public Subnet 2 (CIDR Block: **10.0.2.0/24**)
- Private Subnet 2 (CIDR Block: **10.0.4.0/24**)

Bạn đọc tham khảo source code cho các variable tại [đây](https://github.com/heyyytamvo/AWS-DevOps/blob/main/ECS/AWS-FCJ-WORKSHOP/terraform.tfvars)