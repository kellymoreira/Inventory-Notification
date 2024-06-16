<p align="center">
   <img src="./images/notification-icon.png"  alt="Notification Icon" width="23%"/>
</p>

<h1 align="center">
    Inventory Notification
</h1>

<p align="center">
<b> 
<i>
   Implementing a serverless architecture on AWS to track and notify low inventory levels using <br> S3, Lambda, DynamoDB, and SNS.
</i>
</b>
</p>

## **Scenario**
We're creating an inventory tracking system. 
Stores worldwide will upload inventory files to Amazon S3. 
We want to be able to view the inventory levels and send notifications when inventory levels are low.

###  Process:
- **Upload** an **inventory file** to an **_Amazon S3 bucket_**.
- This upload **triggers** a **_Lambda function_** to read the file and insert items into an **_Amazon DynamoDB_** table.
- A serverless, web-based dashboard application will use **_Amazon Cognito_** for AWS authentication. It will then gain access to the **_DynamoDB_** table to display inventory levels.
- Another **_Lambda function_** will receive updates from the **_DynamoDB_** table. This function will send a message to an **SNS topic** when an inventory item is out of stock.
- **_Amazon SNS_** will then **send** a **notification** through short message service (SMS) or email to request additional inventory.


## **Implementation Overview**
Traditionally, applications run on physical or virtual servers that require purchase, provisioning, and capacity management. 
With **_AWS Lambda_**, you run code without pre-allocated servers, triggered as needed, and pay only for usage.

This setup triggers a **_Lambda function_** when a file is uploaded to **_Amazon S3_**, loading data into an **_Amazon DynamoDB_** table. 
Data is displayed on a dashboard retrieving directly from **_DynamoDB_**. 
This serverless solution scales automatically and incurs minimal costs when idle.

### Objectives:
- Implement a serverless architecture on AWS
- Trigger **_Lambda functions_** from **_Amazon S3_** and **_DynamoDB_**
- Configure **_Amazon SNS_** for notifications

## Final Architecture
![Architecture Overview](/images/architecture-overview.png)