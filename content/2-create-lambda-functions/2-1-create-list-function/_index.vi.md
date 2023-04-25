---
title : "Tạo function liệt kê"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---
Trong phần này chúng ta sẽ tạo function để liệt kê các tài liệu được lưu trong DynamoDB table theo id của người dùng.

1. Mở bảng điều khiển [AWS Lambda](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions) 
2. Ấn nút **Create function**
![LambdaConsole](/images/2-1-create-list-function/2-1-create-list-function-1.png?featherlight=false&width=90pc)

3. Nhập tên function: `list_documents`
- Chọn **Python 3.9** cho mục Runtime
- Nhấn nút **Create function**

![CreateFunction](/images/2-1-create-list-function/2-1-create-list-function-2.png?featherlight=false&width=90pc)

4. Nhập đoạn code sau cho tệp **lambda_function.py**:
```
import json
import boto3
import os
from decimal import *
from boto3.dynamodb.types import TypeDeserializer

dynamodb = boto3.client('dynamodb') 
serializer = TypeDeserializer()

class DecimalEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, Decimal):
            return str(obj)
        return json.JSONEncoder.default(self, obj)

def deserialize(data):
    if isinstance(data, list):
        return [deserialize(v) for v in data]

    if isinstance(data, dict):
        try:
            return serializer.deserialize(data)
        except TypeError:
            return {k: deserialize(v) for k, v in data.items()}
    else:
        return data
        
def lambda_handler(event, context):
    table_name = os.environ['TABLE_NAME']
    user_id = event['pathParameters']['id']
    print(user_id)
    docs = dynamodb.query(
        TableName=table_name,
        KeyConditionExpression="user_id = :id",
        ExpressionAttributeValues={ ":id": { 'S': user_id } }
    )
    
    format_data_docs = deserialize(docs["Items"])
    # TODO implement
    return {
        "statusCode": 200,
        "headers": {
            "Content-Type": "application/json",
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Methods": "GET,PUT,POST,DELETE, OPTIONS",
            "Access-Control-Allow-Headers": "Access-Control-Allow-Headers, Origin,Accept, X-Requested-With, Content-Type, Access-Control-Request-Method,X-Access-Token,XKey,Authorization"
        },
        "body": json.dumps(format_data_docs, cls=DecimalEncoder)
    }

```

- Sau đó ấn **Deploy**

![CreateFunction](/images/2-1-create-list-function/2-1-create-list-function-3.png?featherlight=false&width=90pc)

Đoạn code trên thực hiện lấy biến môi trường **TABLE_NAME** và **id** của người dùng từ event. Sau đó **query** đến DynamoDB table với điều kiện giá trị của **Partition key** bằng id của người dùng. Sau đó định dạng lại dữ liệu được trả về sau khi query.

5. Chúng ta cần thêm biến môi trường cho function. Ấn tab **Configuration**, sau đó chọn **Environment variables** ở menu phía bên trái. Ấn **Edit**

![EnvironmentVariables](/images/2-1-create-list-function/2-1-create-list-function-4.png?featherlight=false&width=90pc)

6. Nhấn nút **Add environment variable**
- Nhập `TABLE_NAME` làm key
- Nhập tên DynamoDB table bạn vừa tạo làm giá trị
- Ấn **Save**

![EnvironmentVariables](/images/2-1-create-list-function/2-1-create-list-function-5.png?featherlight=false&width=90pc)

7. Tiếp theo, thêm quyền cho function truy cập DynamoDB table
- Ấn **Permission** ở bên menu trái
- Ấn vào tên role mà lambda function đang thực hiện

![Permission](/images/2-1-create-list-function/2-1-create-list-function-6.png?featherlight=false&width=90pc)

8. Mở rộng **AWSLambdaBasicExecutionRole...**, sau đó ấn **Edit**

![Permission](/images/2-1-create-list-function/2-1-create-list-function-7.png?featherlight=false&width=90pc)

9. Chọn tab **JSON**. Sao chép đoạn json dưới đây vào editor
```json
,
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:Query"
            ],
            "Resource": "arn:aws:dynamodb:REGION:ACCOUNT_ID:table/Documents"
        }
```

Thay `REGION` và `ACCOUNT_ID` bằng vùng mà bạn tạo bảng và account id của bạn.

- Ấn **Review policy**

![Permission](/images/2-1-create-list-function/2-1-create-list-function-8.png?featherlight=false&width=90pc)

10. Ấn **Save changes**

![Permission](/images/2-1-create-list-function/2-1-create-list-function-9.png?featherlight=false&width=90pc)
