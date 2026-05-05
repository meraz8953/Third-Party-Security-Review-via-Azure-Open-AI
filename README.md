# Azure TPSR Automation POC

An Azure based proof of concept for automating Third Party Security Reviews using Azure Functions, Azure AI Foundry, Azure Blob Storage and Azure Table Storage.

This project demonstrates how a cyber GRC team can process vendor assurance evidence such as SOC 2 reports, SOC 1 reports, ISO 27001 certificates, Statements of Applicability, security questionnaires, policy documents and technical evidence packs. The function extracts evidence, classifies the input, performs an AI assisted review, stores structured JSON output, generates a draft Word report and records metadata in Azure Table Storage.

## Repository topics

`azure-functions` `azure-ai-foundry` `azure-openai` `third-party-risk-management` `tprm` `cyber-grc` `soc2` `soc1` `iso27001` `vendor-risk-management` `python` `azure-storage` `security-assurance`

## What this POC proves

This POC proves a practical end to end TPSR workflow:

1. A user uploads a vendor evidence pack to Azure Blob Storage.
2. Postman or a frontend triggers an Azure Function.
3. The function reads all supported files from the evidence folder.
4. The function extracts readable text from PDF, DOCX, XLSX, CSV, TXT, MD and JSON files.
5. The function classifies the evidence by type.
6. Azure AI Foundry performs an evidence based security review.
7. The output is stored as structured JSON in Blob Storage.
8. A draft Word review report is generated and stored in Blob Storage.
9. Review metadata is written to Azure Table Storage.
10. The API returns review status, risk rating, outcome and storage references.

## Why this matters

Traditional third party security reviews are often manual, inconsistent and time consuming. Reviewers need to read long SOC reports, ISO certificates, questionnaires and policies, then translate them into a defensible GRC assessment.

This POC shows how AI can support that process without replacing reviewer judgement. The AI prepares a draft analysis. A human reviewer still confirms business context, criticality, data sensitivity, residual risk and final approval.

## High level architecture

```text
Vendor evidence pack
        |
        v
Azure Blob Storage
        |
        v
Azure Function HTTP Trigger
        |
        +--> Extract text from PDF, DOCX, XLSX, CSV, TXT, MD, JSON
        |
        +--> Classify evidence type
        |
        +--> Send evidence pack to Azure AI Foundry
        |
        +--> Store AI JSON output in Blob Storage
        |
        +--> Generate draft Word report
        |
        +--> Store metadata in Azure Table Storage
        |
        v
Postman or frontend response
```

## Current POC components

| Component | Technology | Purpose |
| --- | --- | --- |
| Intake simulator | Postman | Sends test review requests |
| Orchestration layer | Azure Functions, Python | Reads files, calls AI, writes outputs |
| AI layer | Azure AI Foundry, GPT model deployment | Performs evidence based review |
| Document store | Azure Blob Storage | Stores input evidence and generated outputs |
| Review register | Azure Table Storage | Stores review metadata and status |
| Report output | Python DOCX generation | Creates a draft review report |

## Supported evidence types

The function can process evidence packs containing:

| Evidence type | Example file types |
| --- | --- |
| SOC 2 Type 2 reports | PDF, DOCX |
| SOC 1 Type 2 reports | PDF, DOCX |
| ISO 27001 certificates | PDF, DOCX |
| Statement of Applicability | XLSX, DOCX, PDF |
| Security questionnaires | XLSX, CSV, DOCX |
| Security policies and standards | DOCX, PDF, TXT, MD |
| Penetration test summaries | PDF, DOCX |
| Vulnerability management evidence | XLSX, CSV, PDF |
| Incident response evidence | DOCX, PDF |
| BCP and DR evidence | DOCX, PDF |
| Privacy and data protection evidence | DOCX, PDF |
| Management or email attestations | TXT, DOCX, PDF |

## Supported file extensions

```text
.pdf
.docx
.xlsx
.csv
.txt
.md
.json
```

Scanned image based PDFs are not supported in this POC. For production, add Azure AI Document Intelligence for OCR.

## Output generated per review

For each review, the function creates a Review ID such as:

```text
TPSR-20260506-ABC12345
```

The output folder in Blob Storage contains:

```text
TPSR-20260506-ABC12345/
  extracted-evidence.txt
  evidence-inventory.json
  ai-output.json
  draft-enterprise-tpsr-review.docx
```

Table Storage stores the review metadata:

| Field | Purpose |
| --- | --- |
| reviewId | Unique review identifier |
| vendorName | Vendor extracted from evidence or supplied by reviewer |
| serviceReviewed | Service or product reviewed |
| reviewType | Initial Review, Annual Review, Renewal Review, etc |
| businessUnit | Business unit supplied in request |
| businessOwner | Business owner supplied in request |
| technicalOwner | Technical owner supplied in request |
| reviewerName | Reviewer supplied in request |
| vendorCriticality | Critical, High, Medium, Low |
| dataSensitivity | Data sensitivity supplied in request |
| status | Completed or Failed |
| overallEvidenceRelianceRating | Strong, Moderate, Limited or Insufficient |
| residualRiskRating | Low, Medium, High, Critical or Review Required |
| finalReviewOutcome | Approved, Approved with Conditions, Further Information Required or Not Approved |
| extractedTextBlobPath | Link path to extracted evidence |
| aiOutputBlobPath | Link path to JSON output |
| wordReportBlobPath | Link path to generated Word report |

## Review logic

The AI prompt follows an evidence agnostic approach:

1. Identify what evidence was received.
2. Assess evidence quality and relevance.
3. Assess evidence adequacy and reliance.
4. Review security control domains.
5. Analyse SOC specific content if present.
6. Analyse ISO 27001 evidence if present.
7. Analyse questionnaire responses if present.
8. Analyse policy and technical documents if present.
9. Identify findings, limitations and compensating factors.
10. Identify shared responsibilities and customer obligations.
11. Create follow up actions only where needed.
12. Draft residual risk and review outcome.

The function does not provide final approval. Final approval remains a reviewer decision.

## Control domains assessed

The draft report assesses:

1. Security governance and assurance
2. Policies and security standards
3. Asset management and data classification
4. Identity and access management
5. Privileged access management
6. Encryption and key management
7. Network and infrastructure security
8. Secure development and change management
9. Vulnerability management and penetration testing
10. Logging, monitoring and incident response
11. Backup, resilience and disaster recovery
12. Third party and subprocessor management
13. Privacy, retention and data disposal
14. Physical and environmental security

## Environment variables

Create these in your Azure Function App settings:

```text
AZURE_OPENAI_ENDPOINT=https://your-ai-resource.openai.azure.com/
AZURE_OPENAI_KEY=your-key
AZURE_OPENAI_DEPLOYMENT=your-deployment-name
AZURE_OPENAI_API_VERSION=2024-12-01-preview
TPSR_STORAGE_CONNECTION_STRING=your-storage-connection-string
TPSR_BLOB_CONTAINER=tpsr-inputs
TPSR_TABLE_NAME=TpsrReviewsTable
```

Do not commit real keys or connection strings.

## Local setup

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
func start
```

For Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
func start
```

## Azure deployment

Deploy using Azure Functions Core Tools or VS Code.

```bash
func azure functionapp publish <your-function-app-name>
```

After deployment, restart the Function App if you changed environment variables.

## Blob Storage folder example

Upload evidence files to:

```text
tpsr-inputs/demo-inputs/software-vendor-pack/
```

Example evidence pack:

```text
demo-inputs/software-vendor-pack/soc2-report.pdf
demo-inputs/software-vendor-pack/iso27001-certificate.pdf
demo-inputs/software-vendor-pack/security-questionnaire.xlsx
demo-inputs/software-vendor-pack/security-policy.docx
demo-inputs/software-vendor-pack/bcp-summary.pdf
```

## Postman request

Method:

```text
POST
```

URL:

```text
https://<your-function-app>.azurewebsites.net/api/tpsr_review?code=<function-key>
```

Headers:

```text
Content-Type: application/json
```

Body:

```json
{
  "sourceType": "evidencePack",
  "inputFolder": "demo-inputs/software-vendor-pack",
  "reviewType": "Initial Review",
  "businessUnit": "Technology",
  "businessOwner": "To be confirmed",
  "technicalOwner": "To be confirmed",
  "reviewerName": "Meraz",
  "vendorCriticality": "High",
  "dataSensitivity": "Confidential business data",
  "organisationUseCase": "The organisation is assessing this vendor for a third party security review. Final service usage must be confirmed by the reviewer.",
  "nextReviewDate": "To be confirmed"
}
```

## Example API response

```json
{
  "reviewId": "TPSR-20260506-ABC12345",
  "status": "Completed",
  "vendorName": "Example Vendor",
  "serviceReviewed": "Example SaaS Platform",
  "overallEvidenceRelianceRating": "Moderate",
  "residualRiskRating": "Medium",
  "draftReviewOutcome": "Approved with Conditions",
  "storage": {
    "blobContainer": "tpsr-inputs",
    "inputFolder": "demo-inputs/software-vendor-pack",
    "extractedTextBlobPath": "TPSR-20260506-ABC12345/extracted-evidence.txt",
    "evidenceInventoryBlobPath": "TPSR-20260506-ABC12345/evidence-inventory.json",
    "aiOutputBlobPath": "TPSR-20260506-ABC12345/ai-output.json",
    "wordReportBlobPath": "TPSR-20260506-ABC12345/draft-enterprise-tpsr-review.docx",
    "tableName": "TpsrReviewsTable",
    "partitionKey": "INITIAL_REVIEW",
    "rowKey": "TPSR-20260506-ABC12345"
  }
}
```

## Production aligned roadmap

This POC is intentionally simple but production aligned.

Recommended next steps:

1. Replace Postman with React, Power Apps or Power Pages frontend.
2. Replace Blob upload simulation with SharePoint document library intake.
3. Replace Table Storage simulation with SharePoint List or Dataverse.
4. Add asynchronous processing so the API returns immediately with Processing status.
5. Add Azure AI Document Intelligence for scanned PDFs and OCR.
6. Add Microsoft Entra authentication and role based access.
7. Add Power Automate approval workflow.
8. Use a controlled Word template with placeholders for final report generation.
9. Add human review and manager approval states.
10. Add audit logging and exception handling dashboards.

## Security considerations

This POC was designed for non sensitive or public test evidence.

Before production use:

1. Use Microsoft Entra authentication.
2. Do not expose function keys to end users.
3. Use managed identity where possible.
4. Use Key Vault for secrets.
5. Apply storage private endpoints if required.
6. Restrict Blob and Table access by RBAC.
7. Enable diagnostic logging.
8. Confirm AI data handling and retention terms.
9. Use approved enterprise AI services only.
10. Avoid sending confidential vendor evidence to unapproved models.

## Human review boundary

The AI output is a draft only.

The reviewer must confirm:

1. Vendor criticality
2. Business use case
3. Data sensitivity
4. Internal control ownership
5. Applicability of CUECs and shared responsibilities
6. Residual risk rating
7. Risk acceptance requirement
8. Final review outcome
9. Approval decision
10. Next review date

## Suggested Git tag

Use this release tag for the first showcase version:

```bash
git tag -a v0.1.0-enterprise-tpsr-poc -m "Enterprise TPSR automation POC using Azure Functions and Azure AI Foundry"
git push origin v0.1.0-enterprise-tpsr-poc
```

## Disclaimer

This project is a proof of concept for cyber GRC automation. It does not replace professional judgement, legal review, privacy review, procurement review or formal risk acceptance processes.
