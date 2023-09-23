---
title : "Create deleting function"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---
In this section, we will create a function to delete document information stored in the DynamoDB table by user id and filename.

1. Open [AWS Lambda console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions) 
2. Click **Create function**
![LambdaConsole](/images/2-3-create-delete-function/2-3-create-delete-function-1.png?featherlight=false&width=90pc)

3. Enter function name: `delete_documents`
- Select **Python 3.11** for Runtime
- Click **Create function**

![CreateFunction](/images/2-3-create-delete-function/2-3-create-delete-function-2.png?featherlight=false&width=90pc)

4. Enter the following code for the **lambda_function.py** file:
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

- Then click **Deploy**

![CreateFunction](/images/2-3-create-delete-function/2-3-create-delete-function-3.png?featherlight=false&width=90pc)

The above code executes to get the **TABLE_NAME** environment variable, **partition key** and **sort key** in request. Then execute the delete command to delete the item in the table with those **partition key** and **sort key**.

5. We need to add an environment variable to the function. Click the **Configuration** tab, then select **Environment variables** in the left menu. Press **Edit**

![EnvironmentVariables](/images/2-3-create-delete-function/2-3-create-delete-function-4.png?featherlight=false&width=90pc)

6. Click **Add environment variable**
- Enter `TABLE_NAME` as key
- Enter the DynamoDB table name that you just created
- Click **Save**

![EnvironmentVariables](/images/2-3-create-delete-function/2-3-create-delete-function-5.png?featherlight=false&width=90pc)

7. Next, add permissions for function to access DynamoDB table
- Click **Permission** on the left menu
- Click on the execution role of the function

![Permission](/images/2-3-create-delete-function/2-3-create-delete-function-6.png?featherlight=false&width=90pc)

8. Expand the **AWSLambdaBasicExecutionRole...** policy, then click **Edit**

![Permission](/images/2-3-create-delete-function/2-3-create-delete-function-7.png?featherlight=false&width=90pc)

9. Copy the JSON below into the editor
```json
,
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:DeleteItem"
            ],
            "Resource": "arn:aws:dynamodb:REGION:ACCOUNT_ID:table/Documents"
        }
```

Replace `REGION` and `ACCOUNT_ID` with the region you create the table and your account id.

- Click **Review policy**

![Permission](/images/2-3-create-delete-function/2-3-create-delete-function-8.png?featherlight=false&width=90pc)

10. Click **Save changes**

![Permission](/images/2-3-create-delete-function/2-3-create-delete-function-9.png?featherlight=false&width=90pc)
