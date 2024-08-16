---
title : "Tạo Public và Private Key"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 2.1.4 </b> "
---

#### Tạo Public và Private Key

Sử dụng **ssh-keygen** để tạo một cặp public và private key. Đặt tên chúng là EC2.

```
ssh-keygen -b 2048 -t rsa
```



{{% notice warning %}}
Nếu source code terraform này là một folder của một Git Repository, hãy thêm cặp key pair này vào file **.gitignore**
{{% /notice %}}

Tạo file **keypair.tf** với cấu hình bên dưới:

```tf
# keypair.tf
resource "aws_key_pair" "EC2key" {
  key_name   = var.ec2_key_name
  public_key = file("${path.module}/EC2.pub")
}
```