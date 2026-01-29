---
layout: default
title: BlobStorageExists
parent: Iris API functions
has_children: false
has_toc: false
---

# BlobStorageExists

Checks if a file exists in Azure Blob Storage for the current tenant.

## Syntax

```jyro
BlobStorageExists(path, fileName)
```

## Parameters

- **path** (string): Path within the container (e.g., `"exports"`)
- **fileName** (string): Name of the file to check (e.g., `"report.xlsx"`)

## Returns

- **boolean**: `true` if the file exists, `false` otherwise

## Description

Performs a lightweight check to determine if a file exists in blob storage without downloading it. Useful for:
- Avoiding duplicate uploads
- Validating file presence before processing
- Checking prerequisites before operations

## Examples

### Basic Existence Check

```jyro
if BlobStorageExists("exports", "report.xlsx") then
    Log("Information", "File already exists")
else
    Log("Information", "File not found")
end
```

### Avoid Duplicate Upload

```jyro
var fileName = "daily_export_" + FormatDate(Now(), "yyyyMMdd") + ".xlsx"

if not BlobStorageExists("exports", fileName) then
    var excelBytes = GenerateExcelBytes(worksheets)
    WriteToBlobStorage("exports", fileName, excelBytes)
    Log("Information", "Created new export: " + fileName)
else
    Log("Information", "Export already exists for today")
end
```

### Check Before Download

```jyro
var fileName = Data.request.query.file

if BlobStorageExists("documents", fileName) then
    var downloadUrl = GetBlobStorageSasUrl("documents", fileName, 60)
    Data._payload = { downloadUrl: downloadUrl }
    Data._statusCode = 200
else
    Data._payload = { error: "File not found" }
    Data._statusCode = 404
end
```

### Validate Prerequisites

```jyro
# Check all required files exist before processing
var requiredFiles = ["config.json", "mapping.json", "template.xlsx"]
var missingFiles = []

foreach file in requiredFiles do
    if not BlobStorageExists("config", file) then
        missingFiles.add(file)
    end
end

if missingFiles.length > 0 then
    Log("Error", "Missing required files: " + Join(missingFiles, ", "))
    CancelAction("Missing required configuration files")
else
    Log("Information", "All required files present, proceeding...")
end
```

### Generate Unique Filename

```jyro
var baseFileName = "report"
var extension = ".xlsx"
var counter = 1
var fileName = baseFileName + extension

# Find a unique filename
while BlobStorageExists("exports", fileName) do
    fileName = baseFileName + "_" + counter + extension
    counter = counter + 1

    # Safety limit
    if counter > 100 then
        Log("Error", "Too many existing files with same name")
        break
    end
end

Log("Information", "Using filename: " + fileName)
WriteToBlobStorage("exports", fileName, excelBytes)
```

## Notes

- This is a lightweight operation that only checks metadata, not content
- Returns `false` if blob storage is not enabled for the tenant
- Useful for conditional logic before more expensive operations
