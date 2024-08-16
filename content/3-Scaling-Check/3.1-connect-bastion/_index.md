---
title : "Connect to Bastion Host"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.1. </b> "
---

![SSMPublicinstance](/images/arc-log.png)
### SSH Agent Forwading

We can connect to EC2 Cluster in private subnet through Bastion Host. However, the last thing we want to do is placing our private key on the Bastion Host. So, we need to use SSH Agent Forwarding. At the folder containing the private key, executing the command line below:


```sh
ssh-add EC2.pem
```

Then, we connect to the Bastion Host by:

```sh
ssh -A ubuntu@<your-bastion-host-public-IP>
```

We can connect to our EC2 Cluster by using this command line:

```sh
ssh ec2-user@<your-EC2Cluster-private-IP>
```

### Validate Scaling Ability

Although this is not the main function of the bastion host. However, you can use Bastion Host to test the scaling ability because of its convenience. Let's validate the scaling ability by sending request to the Load Balancer.

