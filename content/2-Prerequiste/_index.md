---
title : "Preparation"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2. </b> "
---

{{% notice note %}}
You need to install Terraform before going further in this workshop
{{% /notice %}}


Before creating an ECS Cluster, we create some required components such as VPC, Subnets, Internet Gateway, etc. Then, we will create an Auto Scaling Group for ECS EC2 (containers will run on these instances), a Load Balancer to distribute incoming traffic and an ECS Cluster


### Content
  - [Create VPC and Bastion Host](2.1-basic/)
  - [Create Autoscaling Group, Load Balancer, and ECS Cluster](2.2-createalb-asg-ecs/)