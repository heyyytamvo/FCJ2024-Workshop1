---
title : "Problem Definition"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
Containerizing an application and deploying it into a server seems to solve the problem of dependencies since there is more than one application running on a single server. However, there are some issues:

- How does the server handle hundreds or millions of incoming requests?
- Suppose there are 2 Services (A and B) operating on the same server. End-users tend to access Service A more than they do on Service B. So we need to provide computing resources for Service A to avoid any heavy workload.

![ConnectPrivate](/images/1.Intro/00problem.png) 

In this workshop, we will use these services from AWS to solve the problem above:

- **Auto Scaling**: Automatically Scale based on the CPU performance
- **Load Balancer**: Distribute incoming traffic to servers
- **Elastic Container Service**: Utilizing the container management
