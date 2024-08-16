---
title : "Scale up Desired Task"
date :  "`r Sys.Date()`" 
weight : 3 
chapter : false
pre : " <b> 3.2.3 </b> "
---

Ta sẽ tiến hành cập nhật Desired Task tại mục Cluster -> Service và cập nhật số lượng từ 2 thành 10 như hình dưới:

![ConnectPrivate](/images/3.connect/3.2.3/0_Scaling.png)

Sau đó kiểm tra tại Mục Target group, bạn đọc có thể thấy có tổng cộng 10 Containers đang được chạy cùng lúc.

![ConnectPrivate](/images/3.connect/3.2.3/1_Scaling.png)

Để kiểm tra các container trên phân bố như thế nào, bạn đọc kiểm tra tại mục Cluster -> Infrastructure. Như hình bên dưới, 2 EC2 Cluster ban đầu sẽ có 8 container chạy cùng lúc (mỗi EC2 Cluster có 4 container). Đối với EC2 Cluster vừa được khởi tạo, sẽ có 2 Container chạy cùng lúc.

![ConnectPrivate](/images/3.connect/3.2.3/2_Scaling.png)


Lúc này, kiến trúc hạ tầng của ta là hình bên dưới, Load Balancer sẽ phân bố traffic đến các target.

![ConnectPrivate](/images/3.connect/3.2.3/3_Scaling.png)
