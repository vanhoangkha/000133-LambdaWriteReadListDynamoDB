---
title : "Tạo function tạo dữ liệu"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---
Trong phần này chúng ta sẽ tạo function để thêm thông tin tài liệu được lưu trong DynamoDB table.

1. Mở bảng điều khiển [AWS Lambda](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions) 
2. Ấn nút **Create function**
![LambdaConsole](/images/2-2-create-upload-function/2-2-create-upload-function-1.png?featherlight=false&width=90pc)

3. Nhập tên function: `upload_document`
- Chọn **Python 3.9** cho mục Runtime
- Nhấn nút **Create function**

![CreateFunction](/images/2-2-create-upload-function/2-2-create-upload-function-2.png?featherlight=false&width=90pc)

4. Nhập đoạn code sau cho tệp **lambda_function.py**:
```
import json
import boto3
import os
from datetime import datetime, timezone

dynamodb = boto3.resource('dynamodb')
client_cloudwatch = boto3.client('cloudwatch')

def lambda_handler(event, context):
    table_name = os.environ['TABLE_NAME']
    now = datetime.now(tz=timezone.utc)
    dt_string = now.strftime("%d/%m/%Y %H:%M:%S")
    doc_data = json.loads(event["body"])

    path = "protected/{}/{}".format(doc_data['identityId'], doc_data['file'])
    doc_data.update({"path": path, "modified": dt_string})
    table = dynamodb.Table(table_name)
    table.put_item(Item = doc_data)
        
    # TODO implement
    return {
        'statusCode': 200,
        'body': 'successfully upload!',
        'headers': {
            'Content-Type': 'application/json',
            "Access-Control-Allow-Headers": "Access-Control-Allow-Headers, Origin, Accept, X-Requested-With, Content-Type, Access-Control-Request-Method,X-Access-Token, XKey, Authorization",
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Methods": "GET,PUT,POST,DELETE,OPTIONS"
        }
    }
```

- Sau đó ấn **Deploy**

![CreateFunction](/images/2-2-create-upload-function/2-2-create-upload-function-3.png?featherlight=false&width=90pc)

Đoạn code trên thực hiện lấy biến môi trường **TABLE_NAME** và dữ liệu của event. Sau đó thêm từng item vào DynamoDB table.

5. Chúng ta cần thêm biến môi trường cho function. Ấn tab **Configuration**, sau đó chọn **Environment variables** ở menu phía bên trái. Ấn **Edit**

![EnvironmentVariables](/images/2-2-create-upload-function/2-2-create-upload-function-4.png?featherlight=false&width=90pc)

6. Nhấn nút **Add environment variable**
- Nhập `TABLE_NAME` làm key
- Nhập tên DynamoDB table bạn vừa tạo làm giá trị
- Ấn **Save**

![EnvironmentVariables](/images/2-2-create-upload-function/2-2-create-upload-function-5.png?featherlight=false&width=90pc)

7. Tiếp theo, thêm quyền cho function truy cập DynamoDB table
- Ấn **Permission** ở bên menu trái
- Ấn vào tên role mà lambda function đang thực hiện

![Permission](/images/2-2-create-upload-function/2-2-create-upload-function-6.png?featherlight=false&width=90pc)

8. Mở rộng **AWSLambdaBasicExecutionRole...**, sau đó ấn **Edit**

![Permission](/images/2-2-create-upload-function/2-2-create-upload-function-7.png?featherlight=false&width=90pc)

9. Chọn tab **JSON**. Sao chép đoạn json dưới đây vào editor
```json
,
        {
            "Effect": "Allow",
            "Action": "dynamoDB:PutItem",
            "Resource": "arn:aws:dynamodb:REGION:ACCOUNT_ID:table/Documents"
        }
```

Thay `REGION` và `ACCOUNT_ID` bằng vùng mà bạn tạo bảng và account id của bạn.

- Ấn **Review policy**

![Permission](/images/2-2-create-upload-function/2-2-create-upload-function-8.png?featherlight=false&width=90pc)

10. Ấn **Save changes**

![Permission](/images/2-2-create-upload-function/2-2-create-upload-function-9.png?featherlight=false&width=90pc)





