---
layout: default
title: WriteToBlobStorage
parent: Iris API functions
has_children: false
has_toc: false
---

# WriteToBlobStorage

Uploads content to Azure Blob Storage for the current tenant.

## Syntax

```jyro
WriteToBlobStorage(path, fileName, content)
WriteToBlobStorage(path, fileName, content, contentType)
```

## Parameters

- **path** (string): Path within the container (e.g., `"exports/reports"`, `"backups/2024/01"`)
- **fileName** (string): Name of the file (e.g., `"report.xlsx"`, `"data.json"`)
- **content** (any): Content to write - can be a byte array (from `GenerateExcelBytes`) or a string
- **contentType** (string, optional): MIME content type. Auto-detected from file extension if not provided

## Returns

- **boolean**: `true` on success, `false` on failure

## Description

Uploads content to the tenant's Azure Blob Storage account. The file is stored at `{container}/{path}/{fileName}`.

Content types are automatically detected for common extensions:
- `.xlsx` - `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet`
- `.csv` - `text/csv`
- `.json` - `application/json`
- `.xml` - `application/xml`
- `.txt` - `text/plain`
- `.pdf` - `application/pdf`

### Path Sanitization

Paths are automatically sanitized for security:
- Forward and backslashes are normalized
- Parent directory references (`..`) are removed
- Leading/trailing slashes are trimmed

### File Size Limits

Files are limited to the `blobstorage.maxfilesize.mb` property value (default 100MB).

## Examples

### Save Excel Export

```jyro
# Generate Excel file
var worksheets = [{
    name: "Customers",
    data: customerData,
    tableName: "CustomerTable"
}]

var excelBytes = GenerateExcelBytes(worksheets)

# Upload to blob storage
var success = WriteToBlobStorage("exports", "customers.xlsx", excelBytes)

if success then
    Log("Information", "Excel file uploaded successfully")
    var downloadUrl = GetBlobStorageSasUrl("exports", "customers.xlsx", 60)
    Data._payload = { downloadUrl: downloadUrl }
else
    Log("Error", "Failed to upload Excel file")
    Data._payload = { error: "Upload failed" }
    Data._statusCode = 500
end
```

### Organize by Date

```jyro
# Create organized folder structure
var year = FormatDate(Now(), "yyyy")
var month = FormatDate(Now(), "MM")
var fileName = "export_" + FormatDate(Now(), "yyyyMMdd_HHmmss") + ".xlsx"

var path = "exports/" + year + "/" + month
var success = WriteToBlobStorage(path, fileName, excelBytes)

# Result: exports/2024/01/export_20240115_143022.xlsx
```

### Save JSON Data

```jyro
# Store JSON data
var reportData = {
    generatedAt: Now(),
    records: processedRecords,
    summary: {
        total: processedRecords.length,
        success: successCount,
        failed: failedCount
    }
}

var jsonContent = Stringify(reportData)
var success = WriteToBlobStorage("reports", "daily-summary.json", jsonContent, "application/json")
```

### Backup Configuration

```jyro
# Backup tenant configuration
var config = {
    facetDefinitions: GetAllFacetDefinitions(),
    fieldDefinitions: GetAllFieldDefinitions(),
    exportedAt: Now()
}

var backupName = "config_backup_" + FormatDate(Now(), "yyyyMMdd") + ".json"
WriteToBlobStorage("backups", backupName, Stringify(config))
```

## Notes

- Blob storage must be enabled for the tenant (see `IsBlobStorageEnabled`)
- Files are persisted until explicitly deleted (no automatic expiration)
- Each tenant has their own storage account for isolation
- The function logs errors but does not throw exceptions
- Use `GetBlobStorageSasUrl` to generate download URLs for uploaded files
