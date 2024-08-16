---
title : "Deploy Infrastructure"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
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

#### Load Balancer

![ConnectPrivate](/images/2.prerequisite/2.3/0_ALB.png) 
#### Target Group

![ConnectPrivate](/images/2.prerequisite/2.3/1_TG_GR.png) 

#### ECS Cluster
![ConnectPrivate](/images/2.prerequisite/2.3/2_ECS_Cluster.png) 

#### Access Application via Load Balancer DNS
![ConnectPrivate](/images/2.prerequisite/2.3/3_App.png) 