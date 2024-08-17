---
title : "Send 1.000.000 requests to Load Balancer"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.2.2 </b> "
---
Using Bastion Host to send requests as the picture below:

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.2/0_Scaling.png)

Bastion host will send 1.000.000 requests to the Load Balancer. Each time, Bastion Host will send 1000 requests.

After receiving 100.000 requests, ASG has launched a new EC2 Instance as below.

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.2/1_Scaling.png)


We can see that there are 3 registerd EC2 Cluster instead of 2.

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.2/2_Scaling.png)

Now, this is the current architecture:

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.2/3_Scaling.png)

Currently, a new EC2 Cluster is launched. However, the amount of desired tasks is not changed. So, 2 EC2 Cluster still have to handle a heavy workload. To address it, we need to manually scale up the desired tasks. This is the drawback of this workshop: the ECS Desired Task can not scale up automatically.