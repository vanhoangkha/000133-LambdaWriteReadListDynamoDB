---
title : "Create listing function"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---
In this section we will create a function to list the documents stored in the DynamoDB table by the user's id.

1. Open [AWS Lambda console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions) 
2. Click **Create function**
![LambdaConsole](/images/2-1-create-list-function/2-1-create-list-function-1.png?featherlight=false&width=90pc)

3. Enter function name: `list_documents`
- Select **Python 3.11** for Runtime
- Click **Create function**

![CreateFunction](/images/2-1-create-list-function/2-1-create-list-function-2.png?featherlight=false&width=90pc)

4. Enter the following code for the **lambda_function.py** file:
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

- Then click **Deploy**

![CreateFunction](/images/2-1-create-list-function/2-1-create-list-function-3.png?featherlight=false&width=90pc)

The above code executes to get the **TABLE_NAME** and user's **id** environment variables. Then **query** to the DynamoDB table provided that the value of **Partition key** is equal to the user's id. Then reformat the data returned after the query.

5. We need to add an environment variable to the function. Click the **Configuration** tab, then select **Environment variables** in the left menu. Press **Edit**

![EnvironmentVariables](/images/2-1-create-list-function/2-1-create-list-function-4.png?featherlight=false&width=90pc)

6. Click **Add environment variable**
- Enter `TABLE_NAME` as key
- Enter the DynamoDB table name that you just created
- Click **Save**

![EnvironmentVariables](/images/2-1-create-list-function/2-1-create-list-function-5.png?featherlight=false&width=90pc)

7. Next, add permissions for function to access DynamoDB table
- Click **Permission** on the left menu
- Click on the execution role of the function

![Permission](/images/2-1-create-list-function/2-1-create-list-function-6.png?featherlight=false&width=90pc)

8. Expand the **AWSLambdaBasicExecutionRole...** policy, then click **Edit**

![Permission](/images/2-1-create-list-function/2-1-create-list-function-7.png?featherlight=false&width=90pc)

9. Copy the JSON below into the editor
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

Replace `REGION` and `ACCOUNT_ID` with the region you create the table and your account id.

- Click **Review policy**

![Permission](/images/2-1-create-list-function/2-1-create-list-function-8.png?featherlight=false&width=90pc)

10. Click **Save changes**

![Permission](/images/2-1-create-list-function/2-1-create-list-function-9.png?featherlight=false&width=90pc)
