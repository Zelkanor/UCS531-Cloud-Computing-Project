Part 2: The Upload Endpoint
===========================

This guide covers creating the initial API endpoint (POST /upload-url) that allows our client to get a secure S3 upload link.

1\. Create the get\_presigned\_url Lambda
-----------------------------------------

This function securely generates a URL that is valid for one hour, allowing a client to PUT a file directly into our S3 bucket.

1.  **Service:** Lambda
    
2.  **Function Name:** get\_presigned\_url
    
3.  **Runtime:** Python 3.11
    
4.  **Execution Role:** lambda-rag-execution-role (created in Part 1)
    
5.  **Code:** Use the code from your lambda\_functions/get\_presigned\_url.py file. (This code must include the config=Config(signature\_version='s3v4') and endpoint\_url fixes to work in ap-south-1).
    

2\. Create the API Gateway
--------------------------

We will create one central API for our entire project.

1.  **Service:** API Gateway
    
2.  **API Type:** REST API (Build)
    
3.  **API Name:** research-rag-api
    
4.  **Endpoint Type:** Regional
    
5.  Click **Create API**.
    

3\. Create the POST /upload-url Endpoint
----------------------------------------

1.  In your new research-rag-api, select the root (/) resource.
    
2.  Click **Actions** -> **Create Resource**.
    
    *   **Resource Name:** upload-url
        
3.  Click **Create Resource**.
    
4.  With /upload-url selected, click **Actions** -> **Create Method**.
    
5.  Select **POST** and click the checkmark.
    
6.  **Setup Page:**
    
    *   **Integration type:** Lambda Function
        
    *   **Use Lambda Proxy integration:** **CHECK THIS BOX.**
        
    *   **Lambda Function:** get\_presigned\_url
        
7.  Click **Save**. Click **OK** on the permission pop-up.
    

4\. Deploy the API
------------------

Your API is not live until you deploy it.

1.  Click **Actions** -> **Deploy API**.
    
2.  **Deployment stage:** \[New Stage\]
    
3.  **Stage name:** dev
    
4.  Click **Deploy**.
    
5.  **Save your Invoke URL.** It will look like https://\[api-id\].execute-api.ap-south-1.amazonaws.com/dev. This is the API\_BASE\_URL used in the test scripts.