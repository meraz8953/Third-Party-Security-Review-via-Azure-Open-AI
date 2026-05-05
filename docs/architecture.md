# Architecture

## POC flow

```text
Postman or frontend
  -> Azure Function HTTP trigger
  -> Azure Blob Storage evidence folder
  -> Text extraction and evidence classification
  -> Azure AI Foundry
  -> Blob output folder
  -> Azure Table Storage review register
```

## Production aligned version

```text
React or Power Apps frontend
  -> Azure Function API
  -> SharePoint document library
  -> Queue based background processing
  -> Azure AI Foundry or approved enterprise AI model
  -> SharePoint list or Dataverse record
  -> Power Automate approval workflow
  -> Teams or email notification
```

## Why Blob and Table Storage are used in the POC

Blob Storage simulates a SharePoint document library.

Table Storage simulates a SharePoint list or Dataverse table.

This lets the POC stay cheap and simple while keeping the architecture aligned to the future enterprise workflow.
