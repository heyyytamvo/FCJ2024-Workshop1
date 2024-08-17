---
title : "Send 100.000 requests to Load Balancer"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 3.2.1 </b> "
---
Using Bastion Host to send requests as the picture below:

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.1/0_Scaling.png)


Bastion host will send 100.000 requests to the Load Balancer. Each time, Bastion Host will send 1000 requests


After receiving 100.000 requests, 2 EC2 Clusters can handle the request since the Auto Scaling Group did not launch any new EC2 Instance.

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.1/1_Scaling.png)