---
title : "Create Subnet"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2.1.2 </b> "
---

#### Create Public and Private subnet

Create **subnet.tf** with the configuration below:

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

Finally, we have 4 subnets in total as below:

- Public Subnet 1 (CIDR Block: **10.0.1.0/24**)
- Private Subnet 1 (CIDR Block: **10.0.3.0/24**)
- Public Subnet 2 (CIDR Block: **10.0.2.0/24**)
- Private Subnet 2 (CIDR Block: **10.0.4.0/24**)

Please follow this [link](https://github.com/heyyytamvo/AWS-DevOps/blob/main/ECS/AWS-FCJ-WORKSHOP/terraform.tfvars) to check any defined variables