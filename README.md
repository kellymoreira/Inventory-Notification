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

![Inventory Dashboard](/images/inventory-dashboard.png)

If the dashboard application does not display any information, ask your instructor to help you diagnose the problem.

The dashboard application is served as a static webpage from Amazon S3. The dashboard authenticates via Amazon Cognito as an *anonymous user*, which provides sufficient permissions for the dashboard to retrieve data from DynamoDB.

You can also view the data directly in the DynamoDB table.

1. On the **Services** menu, choose **DynamoDB**.
2. In the left navigation pane, choose **Tables**.
3. Choose the **Inventory** table.
4. Choose the **Items** tab.
    
    The data from the inventory file will be displayed. It shows the store, item and inventory count.
    

## **Task 4: Configuring notifications**

You want to notify inventory management staff when a store runs out of stock for an item. For this serverless notification functionality, you will use **Amazon SNS**.

![Task 4: Configuring notifications](/images/task-4.png)

Amazon SNS is a flexible, fully managed publish/subscribe messaging and mobile notifications service. It delivers messages to subscribing endpoints and clients. With Amazon SNS, you can fan out messages to a large number of subscribers, including distributed systems and services, and mobile devices.

33. On the **Services** menu, choose **Simple Notification Service**.
34. In the **Create topic** box, for **Topic name**, enter: `NoStock`. Keep **Standard** selected.
35. Choose **Create topic**
    
    To receive notifications, you must *subscribe* to the topic. You can choose to receive notifications via several methods, such as SMS and email.
    
36. In the lower half of the page, choose **Create subscription** and configure these settings:
    - **Protocol:** *Email*
    - **Endpoint:** Enter your email address
    - Choose **Create subscription**
    
    After you create an email subscription, you will receive a confirmation email message. Open the message and choose the *Confirm subscription* link.
    
    Any message that is sent to the SNS topic will be forwarded to your email.
    

## **Task 5: Creating a Lambda function to send notifications**

You could modify the existing *Load-Inventory* Lambda function to check inventory levels while the file is being loaded. However, this configuration is not a good architectural practice. Instead of overloading the *Load-Inventory* function with business logic, you will create another Lambda function that is triggered when data is loaded into the DynamoDB table. This function will be triggered by a *DynamoDB stream*.

This architectural approach offers several benefits:

- Each Lambda function performs a single, specific function. This practice makes the code simpler and more maintainable.
- Additional business logic can be added by creating additional Lambda functions. Each function operates independently, so existing functionality is not impacted.

In this task, you will create another Lambda function that looks at inventory while it is loaded into the DynamoDB table. If the Lambda function notices that an item is out of stock, it will send a notification through the SNS topic you created earlier.

![Task 5:Creating a Lambda function to send notifications](/images/task-5.png)

39. On the **Services** menu, choose **Lambda**.
40. Choose **Create function** and configure these settings:
    - **Function name:** `Check-Stock`
    - **Runtime:** *Python 3.9*
    - Expand  **Choose or create an execution role**.
    - **Execution role:** *Use an existing role*
    - **Existing role:** *Lambda-Check-Stock-Role*
    - Choose **Create function**
    
    This role was configured with permissions to send a notification to Amazon SNS.
    
41. Scroll down to the **Code source** section, and in the **Environment** pane, choose lambda_function.py.
42. In the code editor, delete all the code.
43. Copy the following code, and in the **Code Source** editor, paste the copied code:

kellymoreira/Inventory-Notification/lambda-functions/sending-notifications
lambda-functions/sending-notifications

Examine the code. It performs the following steps:

- Loop through the incoming records
- If the inventory count is zero, send a message to the *NoStock* SNS topic

You will now configure the function so it triggers when data is added to the *Inventory* table in DynamoDB.

1. Choose **Deploy** to save your code changes
2. Scroll to the **Designer** section (which is at the top of the page).
3. Choose **Add trigger** and then configure these settings:
    - **Select a trigger:** *DynamoDB*
    - **DynamoDB Table:** *Inventory*
    - Choose **Add**
    
    You are now ready to test the system!
    

## **Task 6: Testing the System**

You will now upload an inventory file to Amazon S3, which will trigger the original *Load-Inventory* function. This function will load data into DynamoDB, which will then trigger the new *Check-Stock* Lambda function. If the Lambda function detects an item with zero inventory, it will send a message to Amazon SNS. Then, Amazon SNS will notify you through SMS or email.

47. On the **Services** menu, choose **S3**.
48. Choose the name of your *inventory-* bucket.
49. Choose **Upload** and upload a different inventory file.
50. Return to the **Inventory System Dashboard** and refresh  the page.
    
    You should now be able to use the **Store** menu to view the inventory from both stores.
    
    Also, you should receive a *notification through SMS or email* that the store has an out-of-stock item (each inventory file has one item that is out of stock).
    
    If you did not receive a notification, wait a few minutes and upload a different inventory file. The DynamoDB trigger can sometimes take a few minutes to enable.
    
51. Try to upload multiple inventory files at the same time. What do you think will happen?