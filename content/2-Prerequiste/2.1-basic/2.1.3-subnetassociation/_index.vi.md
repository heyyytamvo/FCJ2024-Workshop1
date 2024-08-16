---
title : "Subnet Association"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 2.1.3 </b> "
---

#### Subnet Association

Vì chúng ta tạo Subnet và VPC thủ công nên chúng ta buộc phải “*đấu nối*” chúng một cách thủ công, sau này bạn đọc có thể dùng [Module](https://registry.terraform.io/modules/terraform-aws-modules/vpc/aws/latest) cho việc này. Nhưng vì bài viết nhắm đến lượng đọc giả không có nền tảng về IaC nên mọi thứ sẽ hoàn toàn là thủ công.

Tạo file **route_table.tf** với cấu hình bên dưới:

```tf
# route_table.tf
resource "aws_route_table" "public_route_table" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = var.vpc_cidr
    gateway_id = "local"
  }

  route {
    cidr_block           = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.defaultIGW.id
  }

  tags = {
    Name = "Public Route Table"
  }
}

resource "aws_route_table" "private_route_table" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "Private Route Table"
  }
}

resource "aws_route" "route_nat_gw" {
  route_table_id         = aws_route_table.private_route_table.id
  destination_cidr_block = "0.0.0.0/0"
  nat_gateway_id         = aws_nat_gateway.my_nat_gtw.id

  timeouts {
    create = "5m"
  }
}

## Subnet Association
resource "aws_route_table_association" "public_subnet_1_associate" {
  subnet_id      = aws_subnet.public_subnet_1.id
  route_table_id = aws_route_table.public_route_table.id
}

## Subnet Association
resource "aws_route_table_association" "public_subnet_2_associate" {
  subnet_id      = aws_subnet.public_subnet_2.id
  route_table_id = aws_route_table.public_route_table.id
}

## Subnet Association
resource "aws_route_table_association" "private_subnet_1_associate" {
  subnet_id      = aws_subnet.private_subnet_1.id
  route_table_id = aws_route_table.private_route_table.id
}

## Subnet Association
resource "aws_route_table_association" "private_subnet_2_associate" {
  subnet_id      = aws_subnet.private_subnet_2.id
  route_table_id = aws_route_table.private_route_table.id
}
```
