---
title : "Create creating function"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---
This section will create a function to add document information stored in the DynamoDB table.

1. Open [AWS Lambda console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions) 
2. Click **Create function**
![LambdaConsole](/images/2-2-create-upload-function/2-2-create-upload-function-1.png?featherlight=false&width=90pc)

3. Enter function name: `upload_document`
- Select **Python 3.11** for Runtime
- Click **Create function**

![CreateFunction](/images/2-2-create-upload-function/2-2-create-upload-function-2.png?featherlight=false&width=90pc)

4. Enter the following code for the **lambda_function.py** file:
```
import json
import boto3
import os
from datetime import datetime, timezone

dynamodb = boto3.resource('dynamodb')

def lambda_handler(event, context):
    table_name = os.environ['TABLE_NAME']
    now = datetime.now(tz=timezone.utc)
    dt_string = now.strftime("%d/%m/%Y %H:%M:%S")
    #doc_data = json.loads(event["body"])
    doc_data = event["body"]

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

- Click **Deploy**

![CreateFunction](/images/2-2-create-upload-function/2-2-create-upload-function-3.png?featherlight=false&width=90pc)

The above code executes to get the environment variables: **TABLE_NAME** and data of request. Then add each item to the DynamoDB table.

5. We need to add an environment variable to the function. Click the **Configuration** tab, then select **Environment variables** in the left menu. Press **Edit**

![EnvironmentVariables](/images/2-2-create-upload-function/2-2-create-upload-function-4.png?featherlight=false&width=90pc)

6. Click **Add environment variable**
- Enter `TABLE_NAME` as key
- Enter the DynamoDB table name that you just created
- Click **Save**

![EnvironmentVariables](/images/2-2-create-upload-function/2-2-create-upload-function-5.png?featherlight=false&width=90pc)

7. Next, add permissions for function to access DynamoDB table
- Click **Permission** on the left menu
- Click on the execution role of the function

![Permission](/images/2-2-create-upload-function/2-2-create-upload-function-6.png?featherlight=false&width=90pc)

8. Expand the **AWSLambdaBasicExecutionRole...** policy, then click **Edit**

![Permission](/images/2-2-create-upload-function/2-2-create-upload-function-7.png?featherlight=false&width=90pc)

9. Copy the JSON below into the editor
```json
,
        {
            "Effect": "Allow",
            "Action": "dynamoDB:PutItem",
            "Resource": "arn:aws:dynamodb:REGION:ACCOUNT_ID:table/Documents"
        }
```

Replace `REGION` and `ACCOUNT_ID` with the region you create the table and your account id.

- Click **Review policy**

![Permission](/images/2-2-create-upload-function/2-2-create-upload-function-8.png?featherlight=false&width=90pc)

10. Click **Save changes**

![Permission](/images/2-2-create-upload-function/2-2-create-upload-function-9.png?featherlight=false&width=90pc)





