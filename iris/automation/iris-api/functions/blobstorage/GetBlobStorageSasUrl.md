---
layout: default
title: GetBlobStorageSasUrl
parent: Iris API functions
has_children: false
has_toc: false
---

# GetBlobStorageSasUrl

Generates a time-limited SAS (Shared Access Signature) URL for downloading a file from blob storage.

## Syntax

```jyro
GetBlobStorageSasUrl(path, fileName)
GetBlobStorageSasUrl(path, fileName, expiryMinutes)
```

## Parameters

- **path** (string): Path within the container (e.g., `"exports"`)
- **fileName** (string): Name of the file (e.g., `"report.xlsx"`)
- **expiryMinutes** (number, optional): Minutes until the URL expires. Default: 60

## Returns

- **string**: SAS URL for downloading the file, or `null` if the file doesn't exist or on error

## Description

Generates a time-limited, read-only URL that allows downloading a specific blob without authentication. The URL:
- Expires after the specified number of minutes
- Is read-only (cannot be used for uploads or deletions)
- Contains a cryptographic signature that cannot be tampered with
- Is safe to share with end users or external systems

### Common Expiry Values

| Minutes | Duration | Use Case |
|---------|----------|----------|
| 5 | 5 minutes | Immediate download, high security |
| 60 | 1 hour | Default, typical download scenario |
| 1440 | 24 hours | Shared links for day-long access |
| 10080 | 7 days | Week-long access for reports |
| 43200 | 30 days | Long-term sharing |

## Examples

### Generate Download Link

```jyro
var downloadUrl = GetBlobStorageSasUrl("exports", "report.xlsx", 60)

if downloadUrl != null then
    Data._payload = {
        downloadUrl: downloadUrl,
        expiresIn: "1 hour"
    }
    Data._statusCode = 200
else
    Data._payload = { error: "File not found" }
    Data._statusCode = 404
end
```

### Upload and Return Download Link

```jyro
# Generate Excel
var excelBytes = GenerateExcelBytes(worksheets)
var fileName = "export_" + FormatDate(Now(), "yyyyMMdd_HHmmss") + ".xlsx"

# Upload to blob storage
var success = WriteToBlobStorage("exports", fileName, excelBytes)

if success then
    # Generate download URL valid for 24 hours
    var downloadUrl = GetBlobStorageSasUrl("exports", fileName, 1440)

    Data._payload = {
        success: true,
        downloadUrl: downloadUrl,
        fileName: fileName,
        expiresIn: "24 hours"
    }
    Data._statusCode = 200
else
    Data._payload = { error: "Failed to save file" }
    Data._statusCode = 500
end
```

### Email Download Link

```jyro
# Generate report
var excelBytes = GenerateExcelBytes(reportWorksheets)
WriteToBlobStorage("reports", "monthly_report.xlsx", excelBytes)

# Generate link valid for 7 days
var downloadUrl = GetBlobStorageSasUrl("reports", "monthly_report.xlsx", 10080)

# Send email with download link
SendEmail(
    "admin@example.com",
    "Monthly Report Ready",
    RenderTemplate("report-email", {
        reportName: "Monthly Report",
        downloadUrl: downloadUrl,
        expiresIn: "7 days"
    })
)
```

### File Browser with Download Links

```jyro
# Dynamic endpoint: GET /api/dynamic/v1/files
var path = Data.request.query.path ?? "exports"
var files = ListBlobStorage(path, "*")

# Add download URLs to each file
var filesWithUrls = []
foreach file in files do
    filesWithUrls.add({
        name: file.name,
        path: file.path,
        sizeBytes: file.sizeBytes,
        lastModified: file.lastModified,
        downloadUrl: GetBlobStorageSasUrl(path, file.name, 60)
    })
end

Data._payload = {
    path: path,
    fileCount: filesWithUrls.length,
    files: filesWithUrls
}
Data._statusCode = 200
```

### Redirect to Download

```jyro
# Dynamic endpoint that redirects to the file download
var fileName = Data.request.query.file

if BlobStorageExists("downloads", fileName) then
    var downloadUrl = GetBlobStorageSasUrl("downloads", fileName, 5)
    Data._redirect = downloadUrl
else
    Data._payload = { error: "File not found" }
    Data._statusCode = 404
end
```

## Security Notes

- SAS URLs are pre-authenticated - anyone with the URL can download the file
- Use short expiry times for sensitive files
- The URL cannot be used after expiration
- The signature is specific to the exact blob path - it cannot be modified to access other files
- Consider logging when SAS URLs are generated for audit purposes

## Notes

- Returns `null` if the file doesn't exist
- The file must exist in blob storage before generating a SAS URL
- Blob storage must be enabled for the tenant (see `IsBlobStorageEnabled`)
- URLs are read-only and cannot be used for uploads or modifications
