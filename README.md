# Inventory Notification

## **Scenario**

We are creating an inventory tracking system. Stores from around the world will upload an inventory file to Amazon S3. Our team wants to be able to view the inventory levels and send a notification when inventory levels are low.

In this situation, we will:

- *Upload* an *inventory file* to an Amazon S3 bucket.
- This upload will *trigger a Lambda function* that will read the file and insert items into an *Amazon DynamoDB table*.
- A serverless, web-based dashboard application will use Amazon Cognito to authenticate to AWS. The application will then gain access to the DynamoDB table to display inventory levels.
- Another Lambda function will receive updates from the DynamoDB table. This function will send a message to an *SNS topic* when an inventory item is out of stock.
- Amazon SNS will then *send you a notification through short message service (SMS) or email* that requests additional inventory.

## **Implementation overview**

Traditionally, applications run on servers. These servers can be physical (or bare metal). They can also be virtual environments that run on top of physical servers. However, you must purchase and provision all these types of servers, and you must also manage their capacity. In contrast, you can run your code on AWS Lambda without needing to pre-allocate servers. With Lambda, you only need to provide the code and define a trigger. The Lambda function can run when it is needed, whether it is once per week or hundreds of times per second. You only pay for what you use.

This action demonstrates how to trigger a Lambda function when a file is uploaded to Amazon Simple Storage Service (Amazon S3). The file will be loaded into an Amazon DynamoDB table. The data will be available for you to view on a dashboard page that retrieves the data directly from DynamoDB. This solution *does not use Amazon Elastic Compute Cloud (Amazon EC2)*. It is a *serverless solution* that *automatically scales when it is used*. It also incurs *little cost* when it is in use. When it is idle, there is *practically no cost* because will you only be billed for data storage.

After completing this solution, we should be able to:

- Implement a serverless architecture on AWS
- Trigger Lambda functions from Amazon S3 and Amazon DynamoDB
- Configure Amazon Simple Notification Service (Amazon SNS) to send notifications

At the **end** of all this, our architecture will look like the following example:
## Architecture overview
![Architecture Overview](/images/architecture-overview.png)

## **Task 1: Creating a Lambda function to load data**
In this task, we will create *a Lambda function* that will process an inventory file. The Lambda function will read the file and insert information into a DynamoDB table.
![Task 1: Creating a Lambda function to load data](/images/task-1.png)


Configure the following settings:

- **Function name:** `Load-Inventory`
- **Runtime:** *Python 3.9*
- Expand  **Choose or create an execution role**.
- **Execution role:** *Use an existing role*
- **Existing role:** *Lambda-Load-Inventory-Role*

This role gives the Lambda function permissions so that it can access Amazon S3 and DynamoDB.

1. Choose **Create function**
2. Scroll down to the **Code source** section, and in the **Environment** pane, choose lambda_function.py.
3. In the code editor, delete all the code.
4. In the **Code source** editor, copy and paste the following code:

kellymoreira/Inventory-Notification/lambda-functions/loading-data
lambda-functions/loading-data

It performs the following steps:

- Download the file from Amazon S3 that triggered the event
- Loop through each line in the file
- Insert the data into the DynamoDB *Inventory* table
1. Choose **Deploy** to save your changes.
    
    Next, you will configure Amazon S3 to trigger the Lambda function when a file is uploaded.
    

## **Task 2: Configuring an Amazon S3 event**
Stores from around the world provide inventory files to load into the inventory tracking system. Instead of uploading their files via FTP, the stores can upload them directly to Amazon S3. They can upload the files through a webpage, a script, or as part of a program. When a file is received, it triggers the Lambda function. This Lambda function will then load the inventory into a DynamoDB table.
![Task 2: Configuring an Amazon S3 event](/images/task-2.png)


