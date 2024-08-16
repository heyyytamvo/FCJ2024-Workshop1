---
title : "Chạy Terraform"
date :  "`r Sys.Date()`" 
weight : 6
chapter : false
pre : " <b> 2.1.6 </b> "
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

#### VPC, Internet Gateway, NAT Gateway, và Route Table

![ConnectPrivate](/images/2.prerequisite/0Overview.png) 

#### Bastion Host

![ConnectPrivate](/images/2.prerequisite/1Bastion.png)

#### Security Group cho Bastion Host

![ConnectPrivate](/images/2.prerequisite/2BastionSG.png)