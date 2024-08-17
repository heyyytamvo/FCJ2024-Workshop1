---
title : "Scale up Desired Task"
date :  "`r Sys.Date()`" 
weight : 3 
chapter : false
pre : " <b> 3.2.3 </b> "
---


We need to update Desired Task at **Cluster -> Service**. Updating desired task from 2 to 10 as below:

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.3/0_Scaling.png)


Then, check the Target Group, we can see that there are 10 containers running at the same time.

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.3/1_Scaling.png)

To check the distribution of the containers, visit **Cluster -> Infrastructure**. As the picture below, we can observe that:
- For the first 2 EC2 Cluster, each EC2 has 4 containers
- For the new EC2 Cluster, it has 2 containers
  
![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.3/2_Scaling.png)


Now, this is the final infrastructure.

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.3/3_Scaling.png)
