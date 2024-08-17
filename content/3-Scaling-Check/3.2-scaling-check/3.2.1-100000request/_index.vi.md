---
title : "Gửi 100.000 requests đến Load Balancer"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 3.2.1 </b> "
---
Sử dụng Bastion Host để gởi 100.000 đến Load Balancer như hình sau:

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.1/0_Scaling.png)

Với câu lệnh trên, Bastion Host sẽ gửi 100.000 requests đến Load Balancer với tần suất 1000 requests/lần gửi.

Sau khi gửi 100.000 requests đến Load Balancer, 2 EC2 Cluster của ta vẫn xử lý được lượng traffic trên khi Auto Scaling Group vẫn chưa khởi tạo thêm bất kỳ EC2 Cluster nào.

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.1/1_Scaling.png)