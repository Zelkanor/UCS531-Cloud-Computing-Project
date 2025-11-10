Part 3: Asynchronous Ingestion Pipeline
=======================================

This is the core pipeline that extracts text from large PDFs. It involves one SNS Topic and three Lambda functions.

1\. Create the SNS Topic
------------------------

This topic acts as a notification service from Textract to our result-handling Lambda.

1.  **Service:** SNS
    
2.  **Type:** Standard
    
3.  **Name:** textract-job-complete
    
4.  Click **Create topic**.
    
5.  **Copy the ARN** for use in your Lambda code and IAM policy.
    

2\. Lambda 1: process-document-batch (Job Starter)
--------------------------------------------------

This function starts the async job.

1.  **Service:** Lambda
    
2.  **Function Name:** process-document-batch
    
3.  **Runtime:** Python 3.11
    
4.  **Execution Role:** lambda-rag-execution-role
    
5.  **Code:** Paste the code from your lambda\_functions/process\_document\_batch.py file.
    
    *   **CRITICAL:** Update the SNS\_TOPIC\_ARN and TEXTRACT\_ROLE\_ARN variables at the top of the file with your real ARNs.
        
6.  **Add S3 Trigger:**
    
    *   Click **\+ Add trigger**.
        
    *   **Source:** S3
        
    *   **Bucket:** rag-project-documents
        
    *   **Event types:** All object creation events
        
    *   **Prefix:** uploads/ (This is crucial to prevent loops)
        
    *   **Suffix:** .pdf
        
    *   Check the acknowledgment box and click **Add**.
        

3\. Lambda 2: handle-textract-result (Result Handler)
-----------------------------------------------------

This function is triggered by SNS when the job is done.

1.  **Service:** Lambda
    
2.  **Function Name:** handle-textract-result
    
3.  **Runtime:** Python 3.11
    
4.  **Execution Role:** lambda-rag-execution-role
    
5.  **Code:** Paste the code from your lambda\_functions/handle\_textract\_result.py file.
    
6.  **Configuration -> General configuration:** Edit and set **Timeout** to **5 minutes**.
    
7.  **Add SNS Trigger:**
    
    *   Click **\+ Add trigger**.
        
    *   **Source:** SNS
        
    *   **Topic:** textract-job-complete
        
    *   Click **Add**.
        

4\. Lambda 3: generate-embeddings (Embedding Generator)
-------------------------------------------------------

This function runs on a schedule to find and process pending chunks.

1.  **Service:** Lambda
    
2.  **Function Name:** generate-embeddings
    
3.  **Runtime:** Python 3.11
    
4.  **Execution Role:** lambda-rag-execution-role
    
5.  **Code:** Paste the code from your lambda\_functions/generate\_embeddings.py file.
    
6.  **Configuration -> General configuration:** Edit and set **Timeout** to **5 minutes**.
    
7.  **Add EventBridge (CloudWatch Events) Trigger:**
    
    *   Click **\+ Add trigger**.
        
    *   **Source:** EventBridge (CloudWatch Events)
        
    *   **Rule:** Create a new rule
        
    *   **Rule name:** embedding-generation-trigger
        
    *   **Schedule expression:** rate(5 minutes)
        
    *   Click **Add**.