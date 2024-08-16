---
title : "Create VPC, Internet Gateway, and NAT Gateway"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 2.1.1 </b> "
---


Creating **vpc.tf** with the configuration below:

```tf
# vpc.tf
resource "aws_vpc" "main" {
  cidr_block                   = var.vpc_cidr
  enable_dns_hostnames         = true
  enable_dns_support           = true
  tags = {
    Name = var.vpc_name
  }
}

resource "aws_internet_gateway" "defaultIGW" {
  vpc_id = aws_vpc.main.id
  tags = {
    Name     = "Workshop 1 Internet Gateway"
  }
}

# Elastic IP for NAT Gateway
resource "aws_eip" "my_elastic_ip" {
  domain   = "vpc"
}

# NAT Gateway
resource "aws_nat_gateway" "my_nat_gtw" {
  allocation_id = aws_eip.my_elastic_ip.id
  subnet_id     = aws_subnet.public_subnet_1.id

  tags = {
    Name = "NAT Gateway"
  }
}
```

First, we create a VPC with a defined CIDR Block (Please follow this [link](https://github.com/heyyytamvo/AWS-DevOps/blob/main/ECS/AWS-FCJ-WORKSHOP/terraform.tfvars) to check any defined variables). Then, we create an Internet Gateway and attach it to our VPC.

Finally, an Elastic IP is created and attached to the NAT Gateway so that private instances can reach the internet.
