Part 4: The RAG Query API
=========================

This guide covers creating the two "read" endpoints (/query and /answer) that allow a user to ask questions.

1\. Lambda 4: handle-query (The Retriever)
------------------------------------------

This function finds the most relevant text chunks.

1.  **Service:** Lambda
    
2.  **Function Name:** handle-query
    
3.  **Runtime:** Python 3.11
    
4.  **Execution Role:** lambda-rag-execution-role
    
5.  **Code:** Paste the code from your lambda\_functions/handle\_query.py file (the final version with the "Robust Body Parsing" fix).
    
6.  **Configuration -> General configuration:** Edit and set **Timeout** to **2 minutes**.
    

2\. Lambda 5: generate-answer (The Generator)
---------------------------------------------

This function uses DeepSeek to synthesize a final answer.

1.  **Service:** Lambda
    
2.  **Function Name:** generate-answer
    
3.  **Runtime:** Python 3.11
    
4.  **Execution Role:** lambda-rag-execution-role
    
5.  **Code:** Paste the code from your lambda\_functions/generate-answer.py file (the final DeepSeek version with the "Robust Body Parsing" fix).
    
6.  **Configuration -> General configuration:** Edit and set **Timeout** to **1 minute**.
    

3\. Connect Endpoints to API Gateway
------------------------------------

1.  **Service:** API Gateway
    
2.  Select your research-rag-api.
    
3.  Select the **root resource (/)** and click **Actions** -> **Create Resource**.
    
    *   **Resource Name:** query
        
    *   Click **Create Resource**.
        
4.  With /query selected, click **Actions** -> **Create Method**.
    
    *   Select **POST** and click the checkmark.
        
    *   **Integration type:** Lambda Function
        
    *   **Check:** Use Lambda Proxy integration
        
    *   **Lambda Function:** handle-query
        
    *   Click **Save** and **OK**.
        
5.  Select the **root resource (/)** again and click **Actions** -> **Create Resource**.
    
    *   **Resource Name:** answer
        
    *   Click **Create Resource**.
        
6.  With /answer selected, click **Actions** -> **Create Method**.
    
    *   Select **POST** and click the checkmark.
        
    *   **Integration type:** Lambda Function
        
    *   **Check:** Use Lambda Proxy integration
        
    *   **Lambda Function:** generate-answer
        
    *   Click **Save** and **OK**.
        

4\. Re-Deploy the API (CRITICAL)
--------------------------------

You must re-deploy your API to make these new endpoints live.

1.  Click **Actions** -> **Deploy API**.
    
2.  **Deployment stage:** Select your existing dev stage.
    
3.  **Description:** Added /query and /answer endpoints
    
4.  Click **Deploy**.