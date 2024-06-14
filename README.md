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



In this task, we will create an S3 bucket and configure it to trigger the Lambda function.

1. On the **Services** menu, choose **S3**.
2. Choose **Create bucket**
    
    Each bucket must have a unique name, so you will add a random number to the bucket name. For example: *inventory-123*
    
3. For **Bucket name** enter: `inventory-<number>` (Replace with a random number)
4. Choose **Create bucket**
    
    You might receive an error that states: *The requested bucket name is not available*. If you get this error, choose the first **Edit** link, change the bucket name, and try again until the bucket name is accepted.
    
    You will now configure the bucket to automatically trigger the Lambda function when a file is uploaded.
    
5. Choose the name of your *inventory-* bucket.
6. Choose the **Properties** tab.
7. Scroll down to **Event notifications**.
    
    You will configure an event to trigger when an object is created in the S3 bucket.
    
8. Click **Create event notification** then configure these settings:
    - **Name:** `Load-Inventory`
    - **Event types:**  *All object create events*
    - **Destination:** *Lambda Function*
    - **Lambda function:** *Load-Inventory*
    - Choose **Save changes**
    
    When an object is created in the bucket, this configuration tells Amazon S3 to trigger the *Load-Inventory* Lambda function that you created earlier.
    
    Your bucket is now ready to receive inventory files!
    

## **Task 3: Testing the loading process**
You are now ready to test the loading process. You will upload an inventory file, then check that it loaded successfully.
![Task 3: Testing the loading process](/images/task-3.png)


Download the inventory files by opening the context (right-click) menu for these links:

kellymoreira/Inventory-Notification/inventory-files
inventory-files

These files are the inventory files that you can use to test the system. They are comma-separated values (CSV) files. The following example shows the contents of the Berlin file:

store,item,count  
Berlin,Echo Dot,12  
Berlin,Echo (2nd Gen),19  
Berlin,Echo Show,18  
Berlin,Echo Plus,0  
Berlin,Echo Look,10  
Berlin,Amazon Tap,15

22. In the console, return to your S3 bucket by choosing the **Objects** tab.
23. Choose **Upload**
24. Choose **Add files**, and select one of the inventory CSV files. (You can choose any inventory file.)
25. Choose **Upload**
    
    Amazon S3 will automatically trigger the Lambda function, which will load the data into a DynamoDB table.
    
    A serverless Dashboard application has been provided for you to view the results.
    
26. At the top of these instructions, choose the **Details** button, and to the right of **AWS**, choose the **Show** button.
27. From the **Credentials** window, copy the **Dashboard** URL.
28. Open a new web browser tab, paste the URL, and press ENTER.
    
    The dashboard application will open and display the inventory data that you loaded into the bucket. The data is retrieved from DynamoDB, which proves that the upload successfully triggered the Lambda function.


