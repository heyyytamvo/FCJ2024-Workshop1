---
title : "Các bước chuẩn bị"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2. </b> "
---

{{% notice note %}}
Bạn đọc cần cài đặt Terraform trước khi có thể hoàn thành bài Workshop này
{{% /notice %}}


Trước khi có thể triển khai một ECS Cluster bằng Terraform, trước hết chúng ta cần tạo VPC và các components cần thiết như: subnet, internet gateway, bastion host, etc. Sau đó chúng ta sẽ khởi tạo Auto Scaling Group cho các ECS EC2 (các container sẽ được chạy trong các EC2 này), Load Balancer để điều phối traffic từ end-users, và ECS Cluster.


### Nội dung
  - [Chuẩn bị VPC và Bastion Host cơ bản](2.1-basic/)
  - [Tạo Autoscaling Group, Load Balancer, và ECS Cluster](2.2-createalb-asg-ecs/)