---
title : "Tạo bảng với DynamoDB"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
#### Tạo bảng với DynamoDB

1. Mở bảng điều khiển của [DynamoDB](https://ap-southeast-1.console.aws.amazon.com/dynamodbv2/home?region=ap-southeast-1#dashboard)
2. Ấn **Create table**

![CreateTable](/images/1-create-table/1-create-table-1.png?featherlight=false&width=90pc)

3. Nhập tên cho bảng: `Documents`
- Nhập **Partition key** là `user_id`
- Nhập **Sort key** là `file`

![CreateTable](/images/1-create-table/1-create-table-2.png?featherlight=false&width=90pc)

4. Tại mục **Table setting**, chọn **Customize settings**
- Giữ nguyên **DynamoDB Standard** cho **Table class**
- Chọn **On-demand** cho **Capacity mode**

![CreateTable](/images/1-create-table/1-create-table-3.png?featherlight=false&width=90pc)

5. Kéo xuống cuối trang, ấn **Create table**

![CreateTable](/images/1-create-table/1-create-table-4.png?featherlight=false&width=90pc)





