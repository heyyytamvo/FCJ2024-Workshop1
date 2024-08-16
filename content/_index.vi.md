---
title : "Triển khai ECS Cluster với Terraform"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---
# Triển khai ECS Cluster với Terraform

### Tổng quan

Trong bài Workshop này, bạn đọc sẽ tìm hiểu cách thức deploy một ứng dụng ReactJS lên hạ tầng AWS, cụ thể ở đây là AWS Elastic Container Service. Tuy nhiên, bạn đọc sẽ không tương tác với AWS Console để triển khai hạ tầng, lý do là bởi có quá nhiều tài nguyên để phải quản lý. Việc thao tác trên Console sẽ rất lâu (ảnh hưởng đến hiệu suất) và sau này việc troubleshoot sẽ rất khó khăn khi chúng ta sẽ không biết bắt đầu từ đâu. Thế là Infrastructure as Code (IaC) ra đời để giúp ta có thể quản lý tài nguyên bằng những file cấu hình, từ đó giúp việc triển khai hạ tầng trên bất cứ nhà cung cấp dịch vụ nào sẽ trở nên khoa học hơn khi chúng ta chỉ cần nhìn vào những file cấu hình để hiểu toàn bộ hạ tầng. Trong bài Workshop này, Terraform sẽ là lựa chọn của ta.

![image](/images/arc-log.png) 

### Kỳ vọng

Sau khi triển khai ECS Cluster, end-user sẽ dùng Load Balancer Domain Name để truy cập đến được app của chúng ta. Application là một VLSM Solver, một bài toán kinh điển trong Networking. Bạn đọc có thể tham khảo sản phẩm đã được triển khai thông qua đường [Link này](http://vlsm.heyyytamvo.io.vn). Hoặc tham khảo source code tại:

+ BackEnd Repo: [Link](https://github.com/heyyytamvo/VLSM-Solver-BE)
+ FrontEnd Repo: [Link](https://github.com/heyyytamvo/VLSM-Solver-FE)

Toàn bộ Source Code cho Workshop này, bạn đọc có thể tham khảo tại [đây](https://github.com/heyyytamvo/AWS-DevOps/tree/main/ECS/AWS-FCJ-WORKSHOP).
### Nội dung

 1. [Đặt vấn đề](1-introduce/)
 2. [Các bước chuẩn bị](2-Prerequiste/)
 3. [Kiểm tra Scaling](3-Scaling-Check/)
 4. [Dọn dẹp tài nguyên](4-cleanup/)
