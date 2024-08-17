---
title : "Triển khai hạ tầng"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---

### Cấp Credentials cho Local Machine

Bạn đọc tham khảo cách lấy Credentials Key thông qua đường [link này](https://docs.aws.amazon.com/cli/v1/userguide/cli-configure-files.html)

### Triển khai hạ tầng với Terraform
Dùng Terminal và trỏ vào thư mục chứa source code terraform ở trên. Thực hiện các command line sau:

```sh
terraform init
terraform plan
terraform apply
```

Đăng nhập vào Console và kiểm tra các tài nguyên, chúng ta sẽ thấy:

#### Load Balancer

![ConnectPrivate](/FCJ2024-Workshop1/images/2.prerequisite/2.3/0_ALB.png) 
#### Target Group

Đây chính là 2 container được khởi chạy trong 2 EC2 Cluster. 2 Container này sẽ đợi traffic từ port 32768 trên EC2 Cluster tương ứng.

![ConnectPrivate](/FCJ2024-Workshop1/images/2.prerequisite/2.3/1_TG_GR.png) 

#### ECS Cluster
![ConnectPrivate](/FCJ2024-Workshop1/images/2.prerequisite/2.3/2_ECS_Cluster.png) 

#### Truy cập Application thông qua Load Balancer DNS
![ConnectPrivate](/FCJ2024-Workshop1/images/2.prerequisite/2.3/3_App.png) 