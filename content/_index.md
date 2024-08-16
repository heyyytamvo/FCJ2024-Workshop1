---
title : "Deploying ECS Cluster with Terraform"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---
# Deploying ECS Cluster with Terraform

### Overview

In this workshop, we will provide step-by-step instructions for deploying a ReactJS Application into AWS Infrastructure, specifically Elastic Container Service (ECS). However, we will not interact with the Console Management since we have to manage too many resources ourselves. Infrastructure as Code (IaC) provides us the ability to provision the infrastructure without manual operation. IaC improves productivity in the process of problem-addressing and troubleshooting since the configuration is well-defined. In this workshop, we will Terraform to deploy our AWS Infrastructure.

![ConnectPrivate](/images/arc-log.png) 

### Expectation

After deploying the ECS Cluster, end-users will use the DNS of the Load Balancer to access the application. Our application is a VLSM Solver, which is a classic problem in computer networking. You can follow this [link](http://vlsm.heyyytamvo.io.vn) to experience the deployed application. For your preference, below are the repositories for the Back End and Front End. 

+ BackEnd Repo: [Link](https://github.com/heyyytamvo/VLSM-Solver-BE)
+ FrontEnd Repo: [Link](https://github.com/heyyytamvo/VLSM-Solver-FE)

In case you want the entire source code, please follow this [link](https://github.com/heyyytamvo/AWS-DevOps/tree/main/ECS/AWS-FCJ-WORKSHOP).

### Content

 1. [Problem Definition](1-introduce/)
 2. [Preparation](2-Prerequiste/)
 3. [Scaling Testing](3-Scaling-Check/)
 4. [Cleanup](4-cleanup/)
