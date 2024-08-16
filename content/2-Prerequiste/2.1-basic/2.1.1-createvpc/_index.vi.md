---
title : "Tạo VPC, Internet Gateway, Và NAT Gateway"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 2.1.1 </b> "
---


Tạo file **vpc.tf** với cấu hình như bên dưới:

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

Theo trình tự, chúng ta sẽ tạo VPC và gán với defined CIDR Block (var.vpc_cidr) (Bạn đọc tham khảo source code cho các variable tại [đây](https://github.com/heyyytamvo/AWS-DevOps/blob/main/ECS/AWS-FCJ-WORKSHOP/terraform.tfvars)). Tiếp đến chúng ta sẽ tạo một Internet Gateway với tên là “*Workshop 1 Internet Gateway*” và gán nó vào VPC.


Cuối cùng ta sẽ tạo một Elastic IP và gán nó cho NAT Gateway (NAT Gateway cần một public IP để đi ra ngoài internet)
