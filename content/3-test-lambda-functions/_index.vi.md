---
title : "Kiểm tra các function"
date: 2024-01-01
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
Trong phần này chúng ta sẽ tạo kiểm tra xem các function có hoạt động đúng hay không.

- Để kiểm tra các function, bạn hãy tải tệp dưới đây về máy:
{{%attachments title="Document Data" pattern=".*\.json$"/%}}

- Chạy câu lệnh sau để ghi dữ liệu vào bảng:
`aws dynamodb batch-write-item --request-items file://documentData.json`


![LambdaConsole](/images/3-test-functions/3-test-functions-1.png?featherlight=false&width=90pc)

#### Kiểm tra function liệt kê
1. Mở bảng điều khiển của function **list_documents**
2. Ấn tab **Test**
- Nhập `tc_1` cho tên event
- Nhập đoạn json dưới đây cho **Event JSON**
```
{
  "pathParameters": {
    "id": "abcd1234"
  }
}
```
3. Ấn **Save**, sau đó ấn **Test**

![LambdaConsole](/images/3-test-functions/3-test-functions-2.png?featherlight=false&width=90pc)

4. Bạn sẽ nhận kết quả trả về là toàn bộ thông tin của các tệp của người dùng với id là **abcd1234**

![LambdaConsole](/images/3-test-functions/3-test-functions-3.png?featherlight=false&width=90pc)

#### Kiểm tra function tạo dữ liệu
1. Mở bảng điều khiển của function **upload_document**
2. Ấn tab **Test**
- Nhập `tc_1` cho tên event
- Nhập đoạn json dưới đây cho **Event JSON**
```
{
  "body": {
      "user_id": "abcd1234",
      "file": "aws_serverless.doc",
      "folder": "",
      "identityId": "123456cvbn",
      "modified": "13-03-2023",
      "size": "2MB",
      "type": "doc",
      "tag": "aws, serverless"
  }
}
```
3. Ấn **Save**, sau đó ấn **Test**

![LambdaConsole](/images/3-test-functions/3-test-functions-4.png?featherlight=false&width=90pc)

4. Bạn sẽ nhận kết quả trả về là **succeeded**

![LambdaConsole](/images/3-test-functions/3-test-functions-5.png?featherlight=false&width=90pc)

5. Mở bảng **Documents** để kiểm tra xem đã thêm thành công hay chưa

![LambdaConsole](/images/3-test-functions/3-test-functions-6.png?featherlight=false&width=90pc)

#### Kiểm tra function xoá dữ liệu
1. Mở bảng điều khiển của function **delete_document**
2. Ấn tab **Test**
- Nhập `tc_1` cho tên event
- Nhập đoạn json dưới đây cho **Event JSON**
```
{
  "pathParameters": {
    "id": "abcd1234"
  },
  "queryStringParameters": {
    "file": "aws-exports.js"
  }
}
```
3. Ấn **Save**, sau đó ấn **Test**

![LambdaConsole](/images/3-test-functions/3-test-functions-7.png?featherlight=false&width=90pc)

4. Bạn sẽ nhận kết quả trả về là **succeeded**

![LambdaConsole](/images/3-test-functions/3-test-functions-8.png?featherlight=false&width=90pc)

5. Mở bảng **Documents** để kiểm tra xem đã xoá thành công hay chưa

![LambdaConsole](/images/3-test-functions/3-test-functions-9.png?featherlight=false&width=90pc)

Vậy là bạn đã hoàn thành tạo các Lambda function tương tác với DynamoDB. Trong bài tiếp theo chúng ta xác thực vào lưu trữ với thư viện Amplify.