---
title : "Chuẩn bị VPC và Bastion Host cơ bản"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 2.1 </b> "
---

Trong bước này, chúng ta sẽ cần tạo một VPC có 2 subnet public / private, một Internet Gateway, và một NAT Gateway. Sau đó, tạo 1 Bastion Host nằm trong public subnet 2.  

### Nội dung
  - [Tạo VPC, Internet Gateway, và NAT Gateway](2.1.1-createvpc/)
  - [Tạo Subnet](2.1.2-createsubnet/)
  - [Subnet Association](2.1.3-subnetassociation/)
  - [Tạo Public và Private Key](2.1.4-createkey/)
  - [Tạo Bastion Host](2.1.5-createbastion/)
  - [Chạy Terraform](2.1.6-terraform-run/)
