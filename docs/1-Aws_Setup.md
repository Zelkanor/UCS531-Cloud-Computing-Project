Part 1: AWS Infrastructure Setup
================================

This guide covers the one-time setup of the core AWS services that store our data and define our permissions.

1\. Amazon S3 Bucket
--------------------

This bucket stores the raw PDF documents uploaded by the user.

*   **Service**: S3
    
*   **Bucket Name**: rag-project-documents
    
*   **Region**: ap-south-1 (Mumbai)
    
*   **Settings**:
    
    *   **Block Public Access**: All settings On (Checked).
        
    *   **Versioning**: Enabled (Recommended).
        

2\. Amazon DynamoDB Table
-------------------------

This table is our vector database. It stores the text chunks and their corresponding embeddings.

*   **Service**: DynamoDB
    
*   **Table Name**: document-chunks
    
*   **Primary Key**:
    
    *   **Partition Key**: chunk\_id (Type: String)
        
    *   **Sort Key**: document\_id (Type: String)
        
*   **Settings**: Default (On-demand capacity).
    

3\. IAM Role
------------

We use a single, comprehensive execution role for all Lambda functions and for the Textract service.

*   **Service**: IAM
    
*   **Role Name**: lambda-rag-execution-role
    

A) Attached AWS-Managed Policies
--------------------------------

Attach the following managed policies to this role:

*   AWSLambdaBasicExecutionRole (For CloudWatch logs)
    
*   AmazonS3FullAccess (Can be locked down, but used for simplicity)
    
*   AmazonDynamoDBFullAccess (Can be locked down)
    
*   AmazonTextractFullAccess
    
*   AmazonBedrockFullAccess
    

B) Custom Managed Policy (For SNS)
----------------------------------

Create a new customer-managed policy to allow this role to publish to our SNS topic (which you will create in Part 3).

*   **Policy Name**: TextractPublishToSNSPolicy
    
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sns:Publish",
            "Resource": "arn:aws:sns:ap-south-1:ACCOUNT_ID:textract-job-complete"
        }
    ]
}
```

**(Note: Replace ACCOUNT\_ID with your 12-digit AWS Account ID)**

Attach this new policy to your lambda-rag-execution-role.

C) Trust Relationship (CRITICAL)
--------------------------------

This is the most important permission. You must edit the role's "Trust relationships" tab to allow both Lambda and Textract to use it.

1.  Go to the lambda-rag-execution-role in the IAM console.
    
2.  Click the **Trust relationships** tab.
    
3.  Click **Edit trust policy**.
    
4.  Replace the existing policy with this one:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": [
                    "lambda.amazonaws.com",
                    "textract.amazonaws.com"
                ]
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```


Click **Update policy**.