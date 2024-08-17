---
title : "Đặt vấn đề"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
Việc containerize một ứng dụng và triển khai nó lên một server duy nhất có vẻ đã giải quyết được bài toán về xung đột dependency khi chúng ta không chỉ triển khai một mà thậm chí rất nhiều application trên cùng một server. Tuy nhiên:

- Làm thế nào để server xử lý một lượng lớn requests từ end-users?
- Việc cấp phát tài nguyên cho từng container sẽ bị bỏ qua: đặt trường hợp có 2 container A và B trên cùng một server, tuy nhiên end-user sẽ truy cập container A với tần suất gấp nhiều lần so với container B. Từ đó, ta cần cấp phát tài nguyên cho A và giảm tài nguyên cho B

![ConnectPrivate](/FCJ2024-Workshop1/images/1.Intro/00problem.png) 

Trong bài Workshop này, chúng ta sẽ tận dụng các dịch vụ từ AWS để giải quyết bài toán trên:

- **Auto Scaling**: Tự động scale up server dựa trên performance của CPU
- **Load Balancer**: Phân phối traffic từ user một cách cân bằng, giảm tải cho các server
- **Elastic Container Service**: Quản lý container một cách chủ động
