Part 5: Testing & Usage
=======================

This project uses the index.ipynb Jupyter Notebook to interact with the API.

1\. Uploading a Document (Cell 1)
---------------------------------

The first part of the notebook contains the upload\_document function.

**Usage:**

1.  Download one of the sample papers (or your own) and place it in the pdfs/ folder.
    
2.  In the last line of the first code cell, call the upload\_document function with the path to your file.
    
3.  Run the cell.
    
4.  **Copy the Document ID** that it prints. You will need this for the next step.
    
```
--- Example of the last line in the cell ---
doc_id = upload_document("pdfs/1706.03762.pdf") # The "Attention Is All You Need" paper
print(f"Upload complete. Save this ID: {doc_id}")
```


After running this, you must wait 5-10 minutes for the asynchronous pipeline (Textract, SNS, chunking, and embedding) to complete.

2\. Testing the Full RAG Pipeline (Cell 2)
------------------------------------------

The second part of the notebook contains the test\_full\_rag\_pipeline function. This script simulates a user query.

**Usage:**

1.  In the last lines of the second code cell, paste the Document ID you copied from the previous step into the MY\_DOCUMENT\_ID variable.
    
2.  Change MY\_QUESTION to whatever you want to ask.
    
3.  Run the cell. It will call /query and /answer, then print the final, AI-generated response and the sources it used.

```
--- Example of the last lines in the cell ---
⬇️ Paste the Document ID you got from Cell 1 ⬇️
MY_DOCUMENT_ID = "6d368fba" # <-- Paste your ID here
MY_QUESTION = "What is a Transformer model?"
test_full_rag_pipeline(MY_QUESTION, MY_DOCUMENT_ID)
```

You can also test querying _all_ documents at once by setting MY\_DOCUMENT\_ID to None.
```
test_full_rag_pipeline("What are the main themes about software engineering?", None)   
```