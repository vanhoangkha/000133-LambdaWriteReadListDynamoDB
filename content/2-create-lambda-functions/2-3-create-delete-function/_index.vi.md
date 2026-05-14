---
title : "Tạo function xoá dữ liệu"
date: 2024-01-01
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---
Trong phần này chúng ta sẽ tạo function để xoá thông tin tài liệu được lưu trong DynamoDB table theo id của người dùng và tên tệp.

1. Mở bảng điều khiển [AWS Lambda](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions) 
2. Ấn nút **Create function**
![LambdaConsole](/images/2-3-create-delete-function/2-3-create-delete-function-1.png?featherlight=false&width=90pc)

3. Nhập tên function: `delete_document`
- Chọn **Python 3.11** cho mục Runtime
- Nhấn nút **Create function**

![CreateFunction](/images/2-3-create-delete-function/2-3-create-delete-function-2.png?featherlight=false&width=90pc)

4. Nhập đoạn code sau cho tệp **lambda_function.py**:
```
import json
import boto3
import os

client = boto3.resource('dynamodb')

def lambda_handler(event, context):
    # TODO implement
    table_name = os.environ['TABLE_NAME']
    error = None
    doc_pk = event['pathParameters']['id']
    print("doc_pk ", doc_pk)
    doc_sk = event['queryStringParameters']['file']
    print("doc_sk ", doc_sk)
    table = client.Table(table_name)
    key = {
        'user_id':doc_pk,
        'file':  doc_sk
    }
    
    try:
        table.delete_item(Key = key)
    except Exception as e:
        error = e
        
        
    except Exception as e:
        error = e
        
    if error is None:
        message = 'delete document successful!'
    else:
        print(error)
        message = 'delete document fail'
    
    return {
            'statusCode': 200,
            'body': message,
            'headers': {
                'Content-Type': 'application/json',
                'Access-Control-Allow-Origin': '*'
            },
        }
```

- Sau đó ấn **Deploy**

![CreateFunction](/images/2-3-create-delete-function/2-3-create-delete-function-3.png?featherlight=false&width=90pc)

Đoạn code trên thực hiện lấy biến môi trường **TABLE_NAME**, **partition key** và **sort key** mà người dùng yêu cầu. Sau đó thực hiện câu lệnh xoá để xoá item trong bảng có **partition key** và **sort key** đó.

5. Chúng ta cần thêm biến môi trường cho function. Ấn tab **Configuration**, sau đó chọn **Environment variables** ở menu phía bên trái. Ấn **Edit**

![EnvironmentVariables](/images/2-3-create-delete-function/2-3-create-delete-function-4.png?featherlight=false&width=90pc)

6. Nhấn nút **Add environment variable**
- Nhập `TABLE_NAME` làm key
- Nhập tên DynamoDB table bạn vừa tạo làm giá trị
- Ấn **Save**

![EnvironmentVariables](/images/2-3-create-delete-function/2-3-create-delete-function-5.png?featherlight=false&width=90pc)

7. Tiếp theo, thêm quyền cho function truy cập DynamoDB table
- Ấn **Permission** ở bên menu trái
- Ấn vào tên role mà lambda function đang thực hiện

![Permission](/images/2-3-create-delete-function/2-3-create-delete-function-6.png?featherlight=false&width=90pc)

8. Mở rộng **AWSLambdaBasicExecutionRole...**, sau đó ấn **Edit**

![Permission](/images/2-3-create-delete-function/2-3-create-delete-function-7.png?featherlight=false&width=90pc)

9. Sao chép đoạn json dưới đây vào editor
```json
,
        {
            "Effect": "Allow",
            "Action": "dynamoDB:DeleteItem",
            "Resource": "arn:aws:dynamodb:REGION:ACCOUNT_ID:table/Documents"
        }
```

Thay `REGION` và `ACCOUNT_ID` bằng vùng mà bạn tạo bảng và account id của bạn.

- Ấn **Review policy**

![Permission](/images/2-3-create-delete-function/2-3-create-delete-function-8.png?featherlight=false&width=90pc)

10. Ấn **Save changes**

![Permission](/images/2-3-create-delete-function/2-3-create-delete-function-9.png?featherlight=false&width=90pc)



