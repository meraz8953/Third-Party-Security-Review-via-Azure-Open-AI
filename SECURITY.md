# Security Notes

This repository is a proof of concept. Do not use real confidential vendor evidence unless your organisation has approved the AI service, storage design and data handling model.

## Do not commit secrets

Never commit:

1. Azure OpenAI keys
2. Storage account connection strings
3. Function keys
4. Vendor reports
5. Customer data
6. Internal policies or confidential questionnaires

## Recommended production controls

1. Use Microsoft Entra authentication.
2. Use managed identity instead of static connection strings.
3. Store secrets in Azure Key Vault.
4. Restrict Blob and Table access using RBAC.
5. Enable private endpoints if required.
6. Add diagnostic logging.
7. Add data retention and purge controls.
8. Add human review and approval before final risk decision.
9. Confirm AI data handling terms before production use.
