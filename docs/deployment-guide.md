# Deployment Guide

## Prerequisites

1. Azure subscription
2. Azure Function App using Python
3. Azure Storage Account
4. Azure AI Foundry or Azure OpenAI model deployment
5. Azure Functions Core Tools
6. Postman for testing

## 1. Create storage resources

Create a Blob container:

```text
tpsr-inputs
```

Create a Table Storage table:

```text
TpsrReviewsTable
```

The function can also create these automatically if the connection string has permission.

## 2. Add Function App settings

Add these app settings in the Azure Function App:

```text
AZURE_OPENAI_ENDPOINT
AZURE_OPENAI_KEY
AZURE_OPENAI_DEPLOYMENT
AZURE_OPENAI_API_VERSION
TPSR_STORAGE_CONNECTION_STRING
TPSR_BLOB_CONTAINER
TPSR_TABLE_NAME
```

Restart the Function App after changing environment variables.

## 3. Deploy

From the repo root:

```bash
func azure functionapp publish <your-function-app-name>
```

## 4. Upload evidence pack

Upload evidence files to:

```text
tpsr-inputs/demo-inputs/software-vendor-pack/
```

## 5. Test with Postman

POST to:

```text
https://<function-app>.azurewebsites.net/api/tpsr_review?code=<function-key>
```

Use the JSON body in `sample_requests/postman_evidence_pack_request.json`.

## 6. Validate outputs

Check Blob Storage for:

```text
TPSR-*/extracted-evidence.txt
TPSR-*/evidence-inventory.json
TPSR-*/ai-output.json
TPSR-*/draft-enterprise-tpsr-review.docx
```

Check Table Storage for the metadata row.
