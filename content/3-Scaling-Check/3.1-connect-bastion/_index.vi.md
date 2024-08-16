---
title : "Kết nối đến Bastion Host"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.1. </b> "
---

![SSMPublicinstance](/images/arc-log.png)
### SSH Agent Forwading

Như Architecture ở trên, ta có thể kết nối đến những EC2 Cluster thông qua Bastion Host. Tuy nhiên, ta không hề mong muốn Bastion Host chứa Private Key. Và thế là ta dùng SSH Agent Forwarding. Tại folder có chứa public key mà ta vừa tạo ở phần trước, thực hiện câu lệnh:

```sh
ssh-add EC2.pem
```

Sau đó ta có thể kết nối với Bastion Host thông qua câu lệnh:

```
ssh -A ubuntu@<your-bastion-host-public-IP>
```

Giờ ta đã kết nối thành công với Bastion Host. Giờ ta có thể kết nối đến EC2 Cluster thông qua câu lệnh sau:

```sh
ssh ec2-user@<your-EC2Cluster-private-IP>
```

### Kiểm tra Scaling

Quay trở lại với Bastion Host, ta sẽ dùng Bastion Host để kiểm tra độ Scaling của các EC2 Cluster. Tất nhiên đây không phải là chức năng chính của Bastion Host, nhưng vì thuận tiện nên bạn đọc có thể dùng Bastion Host để gởi request đến Load Balancer và kiểm tra độ Scaling của EC2 Cluster.
