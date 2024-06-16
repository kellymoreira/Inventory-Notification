ᴇɴɢʟɪꜱʜ | [ᴘᴏʀᴛᴜɢᴜᴇꜱᴇ-ʙʀ](https://github.com/kellymoreira/Inventory-Notification/blob/main/README_pt-br.md)

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

<!--
<p align="center">
    <samp>
    <a href="#About">ABOUT</a>
   | <a href="#Technologies">TECHNOLOGIES</a>
   | <a href="#Usage">USAGE</a>
   | <a href="#Resources">RESOURCES</a>
   | <a href="#Documentation">DOCUMENTATION</a>
   | <a href="#Feedbacks">FEEDBACKS</a>
     </samp>
</p>
-->

<!------------------------------------------------------------
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

---------------------->

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


<!----------------------------------- TASK 1 -----------------------------------

## **Task 1: Creating a Lambda function to load data**
In this task, we will create *a Lambda function* that will process an inventory file. The Lambda function will read the file and insert information into a DynamoDB table.

<details>
    <summary><strong>Expand for Details</strong></summary>
    <p>Configure the following settings:</p>
    <ul>
        <li><strong>Function name:</strong> <code>Load-Inventory</code></li>
        <li><strong>Runtime:</strong> <em>Python 3.9</em></li>
        <li>Expand <strong>Choose or create an execution role</strong>.</li>
        <li><strong>Execution role:</strong> <em>Use an existing role</em></li>
        <li><strong>Existing role:</strong> <em>Lambda-Load-Inventory-Role</em></li>
    </ul>
    <p>This role gives the Lambda function permissions so that it can access Amazon S3 and DynamoDB.</p>
    <ol>
        <li>Choose <strong>Create function</strong></li>
        <li>Scroll down to the <strong>Code source</strong> section, and in the <strong>Environment</strong> pane, choose <code>lambda_function.py</code>.</li>
        <li>In the code editor, delete all the code.</li>
        <li>In the <strong>Code source</strong> editor, copy and paste the following code:</li>
    </ol>
    <pre>
        <code>kellymoreira/Inventory-Notification/lambda-functions/loading-data
        lambda-functions/loading-data</code>
    </pre>
    <p>It performs the following steps:</p>
    <ul>
        <li>Download the file from Amazon S3 that triggered the event</li>
        <li>Loop through each line in the file</li>
        <li>Insert the data into the DynamoDB <em>Inventory</em> table</li>
    </ul>
    <ol start="5">
        <li>Choose <strong>Deploy</strong> to save your changes.</li>
    </ol>
    <p>Next, you will configure Amazon S3 to trigger the Lambda function when a file is uploaded.</p>
    
![Task 1: Creating a Lambda function to load data](/images/task-1.png)

</details>

<!----------------------------------- TASK 2 -----------------------------------

## **Task 2: Configuring an Amazon S3 event**
Stores from around the world provide inventory files to load into the inventory tracking system. Instead of uploading their files via FTP, the stores can upload them directly to Amazon S3. They can upload the files through a webpage, a script, or as part of a program. When a file is received, it triggers the Lambda function. This Lambda function will then load the inventory into a DynamoDB table.

In this task, we will create an S3 bucket and configure it to trigger the Lambda function.

<details>
    <summary><strong>Expand for Details</strong></summary>
    <ol>
        <li>On the <strong>Services</strong> menu, choose <strong>S3</strong>.</li>
        <li>Choose <strong>Create bucket</strong></li>
        <p>Each bucket must have a unique name, so you will add a random number to the bucket name. For example: <em>inventory-123</em></p>
        <li>For <strong>Bucket name</strong> enter: <code>inventory-<number></code> (Replace with a random number)</li>
        <li>Choose <strong>Create bucket</strong></li>
        <p>You might receive an error that states: <em>The requested bucket name is not available</em>. If you get this error, choose the first <strong>Edit</strong> link, change the bucket name, and try again until the bucket name is accepted.</p>
        <p>You will now configure the bucket to automatically trigger the Lambda function when a file is uploaded.</p>
        <li>Choose the name of your <em>inventory-</em> bucket.</li>
        <li>Choose the <strong>Properties</strong> tab.</li>
        <li>Scroll down to <strong>Event notifications</strong>.</li>
        <p>You will configure an event to trigger when an object is created in the S3 bucket.</p>
        <li>Click <strong>Create event notification</strong> then configure these settings:
            <ul>
                <li><strong>Name:</strong> <code>Load-Inventory</code></li>
                <li><strong>Event types:</strong> <em>All object create events</em></li>
                <li><strong>Destination:</strong> <em>Lambda Function</em></li>
                <li><strong>Lambda function:</strong> <em>Load-Inventory</em></li>
            </ul>
        </li>
        <li>Choose <strong>Save changes</strong></li>
        <p>When an object is created in the bucket, this configuration tells Amazon S3 to trigger the <em>Load-Inventory</em> Lambda function that you created earlier.</p>
    </ol>
    <p>Your bucket is now ready to receive inventory files!</p>

![Task 2: Configuring an Amazon S3 event](/images/task-2.png)
            
</details>


<!----------------------------------- TASK 3 -----------------------------------

## **Task 3: Testing the loading process**
We are now ready to test the loading process. You will upload an inventory file, then check that it loaded successfully.

<details> 
    <summary><strong>Expand for Details</strong></summary>        
    <ol>
        <li>Download the inventory files by opening the context (right-click) menu for these links:
            <ul>
                <li><code>kellymoreira/Inventory-Notification/inventory-files</code></li>
                <li><code>inventory-files</code></li>
            </ul>
            These files are the inventory files that you can use to test the system. They are comma-separated values (CSV) files. The following example shows the contents of the Berlin file:
            <pre><code>  
store,item,count  
Berlin,Echo Dot,12  
Berlin,Echo (2nd Gen),19  
Berlin,Echo Show,18  
Berlin,Echo Plus,0  
Berlin,Echo Look,10  
Berlin,Amazon Tap,15
            </code></pre>
        </li>
        <li>In the console, return to your S3 bucket by choosing the <strong>Objects</strong> tab.</li>
        <li>Choose <strong>Upload</strong></li>
        <li>Choose <strong>Add files</strong>, and select one of the inventory CSV files. (You can choose any inventory file.)</li>
        <li>Choose <strong>Upload</strong></li>
        <p>Amazon S3 will automatically trigger the Lambda function, which will load the data into a DynamoDB table.</p>
        <p>A serverless Dashboard application has been provided for you to view the results.</p>
        <li>At the top of these instructions, choose the <strong>Details</strong> button, and to the right of <strong>AWS</strong>, choose the <strong>Show</strong> button.</li>
        <li>From the <strong>Credentials</strong> window, copy the <strong>Dashboard</strong> URL.</li>
        <li>Open a new web browser tab, paste the URL, and press ENTER.</li>
        <p>The dashboard application will open and display the inventory data that you loaded into the bucket. The data is retrieved from DynamoDB, which proves that the upload successfully triggered the Lambda function.</p>
        ![Inventory Dashboard](/images/inventory-dashboard.png)
        <p>The dashboard application is served as a static webpage from Amazon S3. The dashboard authenticates via Amazon Cognito as an <em>anonymous user</em>, which provides sufficient permissions for the dashboard to retrieve data from DynamoDB.</p>
        <p>You can also view the data directly in the DynamoDB table.</p>
        <li>On the <strong>Services</strong> menu, choose <strong>DynamoDB</strong>.</li>
        <li>In the left navigation pane, choose <strong>Tables</strong>.</li>
        <li>Choose the <strong>Inventory</strong> table.</li>
        <li>Choose the <strong>Items</strong> tab.</li>
        <p>The data from the inventory file will be displayed. It shows the store, item, and inventory count.</p>
        ![Task 3: Testing the loading process](/images/task-3.png)
    </ol>
</details>

<!----------------------------------- TASK 4 -----------------------------------

## **Task 4: Configuring notifications**
You want to notify inventory management staff when a store runs out of stock for an item. For this serverless notification functionality, you will use **Amazon SNS**.

Amazon SNS is a flexible, fully managed publish/subscribe messaging and mobile notifications service. It delivers messages to subscribing endpoints and clients. With Amazon SNS, you can fan out messages to a large number of subscribers, including distributed systems and services, and mobile devices.

<details>
    <summary><strong>Expand for Details</strong></summary>
    <ol>
        <li>On the <strong>Services</strong> menu, choose <strong>Simple Notification Service</strong>.</li>
        <li>In the <strong>Create topic</strong> box, for <strong>Topic name</strong>, enter: <code>NoStock</code>. Keep <strong>Standard</strong> selected.</li>
        <li>Choose <strong>Create topic</strong>.</li>
        <p>To receive notifications, you must <em>subscribe</em> to the topic. You can choose to receive notifications via several methods, such as SMS and email.</p>
        <li>In the lower half of the page, choose <strong>Create subscription</strong> and configure these settings:
            <ul>
                <li><strong>Protocol:</strong> <em>Email</em></li>
                <li><strong>Endpoint:</strong> Enter your email address</li>
            </ul>
        </li>
        <li>Choose <strong>Create subscription</strong></li>
        <p>After you create an email subscription, you will receive a confirmation email message. Open the message and choose the <em>Confirm subscription</em> link.</p>
        <p>Any message that is sent to the SNS topic will be forwarded to your email.</p>
        <img src="/images/task-4.png" alt="Task 4: Configuring notifications">
    </ol>
</details>
    
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
    
51. Try to upload multiple inventory files at the same time. What do you think will happen? >
