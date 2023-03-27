---
title : "Test lambda functions"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
In this section we will create tests to see if the functions are working properly.

To test the functions, download the following file to your computer and run the command:
`aws dynamodb batch-write-item --request-items file://documentData.json`

{{%attachments title="Document Data" pattern=".*\.json$"/%}}

![LambdaConsole](/images/3-test-functions/3-test-functions-1.png?featherlight=false&width=90pc)

#### Test listing functions
1. Open the **list_documents** function console
2. Click **Test** tab
- Enter `tc_1` for event name
- Enter the below json for **Event JSON**
```
{
  "pathParameters": {
    "id": "abcd1234"
  }
}
```
3. Click **Save**, then click **Test**

![LambdaConsole](/images/3-test-functions/3-test-functions-2.png?featherlight=false&width=90pc)

4. You will get all the information of the user's files with the id **abcd1234**

![LambdaConsole](/images/3-test-functions/3-test-functions-3.png?featherlight=false&width=90pc)

#### Test creating function
1. Open the **upload_documents** function console
2. Click **Test** tab
- Enter `tc_1` for event name
- Enter the below json for **Event JSON**
```
{
  "body": [
    {
      "user_id": "abcd1234",
      "file": "aws_serverless.doc",
      "folder": "",
      "identityId": "123456cvbn",
      "modified": "13-03-2023",
      "size": "2MB",
      "type": "doc",
      "tag": "aws, serverless"
    }
  ]
}
```
3. Click **Save**, then click**Test**

![LambdaConsole](/images/3-test-functions/3-test-functions-4.png?featherlight=false&width=90pc)

4. You will get a return result of **succeeded**

![LambdaConsole](/images/3-test-functions/3-test-functions-5.png?featherlight=false&width=90pc)

5. Open **Documents** table to check if added successfully

![LambdaConsole](/images/3-test-functions/3-test-functions-6.png?featherlight=false&width=90pc)

#### Test deleting function
1. Opent the **delete_document** function console
2. Click **Test** tab
- Enter `tc_1` for event name
- Enter the below json for **Event JSON**
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
3. Click **Save**, then click**Test**

![LambdaConsole](/images/3-test-functions/3-test-functions-7.png?featherlight=false&width=90pc)

4. You will get a return result of **succeeded**

![LambdaConsole](/images/3-test-functions/3-test-functions-8.png?featherlight=false&width=90pc)

5. Open **Documents** table to check if deleted successfully

![LambdaConsole](/images/3-test-functions/3-test-functions-9.png?featherlight=false&width=90pc)

You are done creating Lambda functions that interact with DynamoDB. In the next post we will authenticate to the archive with the Amplify library.