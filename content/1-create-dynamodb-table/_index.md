---
title : "Create DynamoDB table"
date: 2024-01-01
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
#### Create DynamoDB table

1. Open [DynamoDB console](https://ap-southeast-1.console.aws.amazon.com/dynamodbv2/home?region=ap-southeast-1#dashboard)
2. Click **Create table**

![CreateTable](/images/1-create-table/1-create-table-1.png?featherlight=false&width=90pc)

3. Enter table name: `Documents`
- Enter **Partition key** is `user_id`
- Enter **Sort key** is `file`

![CreateTable](/images/1-create-table/1-create-table-2.png?featherlight=false&width=90pc)

4. In **Table setting** section, select **Customize settings**
- Keep **DynamoDB Standard** for **Table class**
- Select **On-demand** for **Capacity mode**

![CreateTable](/images/1-create-table/1-create-table-3.png?featherlight=false&width=90pc)

5. Scroll to the bottom of the page, click **Create table**

![CreateTable](/images/1-create-table/1-create-table-4.png?featherlight=false&width=90pc)





