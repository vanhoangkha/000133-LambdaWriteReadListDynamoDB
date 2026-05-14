---
title : "Dọn dẹp tài nguyên"
date: 2024-01-01
weight : 4
chapter : false
pre : " <b> 4. </b> "
---
{{% notice note %}}
Nếu bạn tiếp tục với bài số 2 của series thì nên bỏ qua bước xoá Lambda function.
{{% /notice %}}

1. Xoá bảng trong DynamoDB
- Mở bảng điều khiển của [DynamoDB](https://ap-southeast-2.console.aws.amazon.com/dynamodbv2/home?region=ap-southeast-2#dashboard)
- Chọn **Tables** ở menu phía bên trái
- Chọn bảng **Books**
- Ấn **Delete**
- Nhập **delete** và ấn **Delete table**
2. Xoá Lambda function
- Mở bảng điều khiển của [AWS Lambda](https://ap-southeast-2.console.aws.amazon.com/lambda/home?region=ap-southeast-2#/functions)
- Chọn **list_documents** function
- Ấn **Actions**
- Chọn **Delete**
- Nhập **delete** và ấn **Delete**
- Tương tự với **upload_document** và **delete_document** function