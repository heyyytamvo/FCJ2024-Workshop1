---
title : "Create Public and Private Key"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 2.1.4 </b> "
---

#### Create Public and Private Key

Using **ssh-keygen** to create a key pair and name it *EC2*.

```
ssh-keygen -b 2048 -t rsa
```

{{% notice warning %}}
If this key pair belongs to a Git Repository, please add it to **.gitignore** to avoid leaking credentials.

{{% /notice %}}

Create **keypair.tf** with the configuration below:

```tf
# keypair.tf
resource "aws_key_pair" "EC2key" {
  key_name   = var.ec2_key_name
  public_key = file("${path.module}/EC2.pub")
}
```