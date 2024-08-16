---
title : "Execute Terraform"
date :  "`r Sys.Date()`" 
weight : 6
chapter : false
pre : " <b> 2.1.6 </b> "
---

### Providing Credentials for Local Machine

Using this [link](https://docs.aws.amazon.com/cli/v1/userguide/cli-configure-files.html) to configure your credentials

### Provision AWS Infrastructure with Terraform
Using Terminal and executing these command lines:

```sh
terraform init
terraform plan
terraform apply
```

Login to Console Management and check the resources:

#### VPC, Internet Gateway, NAT Gateway, and Route Table

![ConnectPrivate](/images/2.prerequisite/0Overview.png) 

#### Bastion Host

![ConnectPrivate](/images/2.prerequisite/1Bastion.png)

#### Security Group for Bastion Host

![ConnectPrivate](/images/2.prerequisite/2BastionSG.png)