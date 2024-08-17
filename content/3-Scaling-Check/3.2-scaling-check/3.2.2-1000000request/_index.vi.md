---
title : "Gửi 1.000.000 requests đến Load Balancer"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.2.2 </b> "
---
Sử dụng Bastion Host để gởi 1.000.000 đến Load Balancer như hình sau:

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.2/0_Scaling.png)

Với câu lệnh trên, Bastion Host sẽ gửi 1.000.000 requests đến Load Balancer với tần suất 1000 requests/lần gửi.

Sau khi gửi 1.000.000 requests đến Load Balancer, Auto Scaling Group đã tiến hành khởi chạy thêm một EC2 Cluster như hình bên dưới.

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.2/1_Scaling.png)

Chuyển sang **ECS Cluster**, bạn đọc có thể thấy tổng cộng 3 EC2 Cluster được registered vào ECS Cluster thay vì là 2 như ban đầu.

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.2/2_Scaling.png)

Lúc này, kiến trúc hạ tầng hiện tại của ta sẽ là hình bên dưới:

![ConnectPrivate](/FCJ2024-Workshop1/images/3.connect/3.2.2/3_Scaling.png)

Hiện tại, một EC2 Cluster đã được khởi tạo, tuy nhiên, lượng Task (hay Container tương ứng) không đổi nên 2 EC2 Cluster ban đầu vẫn đang phải chịu tải rất lớn. Do đó ta cần tiến hành scale up lượng Desired Task thủ công. Đây cũng chính là nhược điểm mà Workshop này chưa thể cover được.