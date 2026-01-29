---
layout: default
title: UploadFileToSharePoint
parent: Iris API functions
has_children: false
has_toc: false
---

# UploadFileToSharePoint

Uploads a file from Azure Blob Storage to SharePoint Online with optional metadata.

## Syntax

```jyro
UploadFileToSharePoint(config, containerName, blobPath, metadata)
UploadFileToSharePoint(config, containerName, blobPath, metadata, targetFileName)
```

## Parameters

- **config** (object): SharePoint connection configuration
- **containerName** (string): Source Azure blob container name
- **blobPath** (string): Source blob path
- **metadata** (array): Array of key-value pairs for SharePoint column values
- **targetFileName** (string, optional): Override filename (defaults to blob filename)

## Configuration Object

The config object must contain:

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| siteUrl | string | Yes | Full SharePoint site URL (e.g., `https://tenant.sharepoint.com/sites/MySite`) |
| folderPath | string | Yes | Server-relative folder path (e.g., `/sites/MySite/Shared Documents/Folder`) |
| clientId | string | Yes | Azure AD application client ID |
| tenantId | string | Yes | Azure AD tenant ID |
| certificateBase64 | string | Yes | Base64-encoded .pfx certificate |
| certificatePassword | string | No | Password for the certificate |
| listItemType | string | No | SharePoint list item type (default: `SP.Data.Shared_x0020_DocumentsItem`) |
| autoCreateColumns | boolean | No | Auto-create missing columns (default: `true`) |

## Metadata Array

Each metadata item should be an object with:
- **key** (string): SharePoint column name
- **value** (string): Value to set

## Returns

- **object**: Result object with properties:
  - **success** (boolean): Whether upload succeeded
  - **fileUrl** (string): URL of the uploaded file (on success)
  - **error** (string): Error message (on failure)

## Description

Uploads files from Azure Blob Storage to SharePoint Online document libraries. Uses certificate-based authentication for secure server-to-server communication.

### Features

- **Automatic column creation**: When `autoCreateColumns` is true, missing SharePoint columns are automatically created as text columns via Microsoft Graph API
- **Metadata updates**: Set custom column values on uploaded files
- **File overwrite**: Existing files with the same name are overwritten

### Required Azure AD Permissions

The Azure AD application needs:
- `Sites.ReadWrite.All` - For file upload and metadata updates
- `Sites.Manage.All` - For automatic column creation (if enabled)

## Examples

### Basic file upload

```jyro
var config = {
    siteUrl: "https://contoso.sharepoint.com/sites/Documents",
    folderPath: "/sites/Documents/Shared Documents/Uploads",
    clientId: GetSecret("SP_CLIENT_ID"),
    tenantId: GetSecret("SP_TENANT_ID"),
    certificateBase64: GetSecret("SP_CERTIFICATE_BASE64"),
    certificatePassword: GetSecret("SP_CERTIFICATE_PASSWORD")
}

var result = UploadFileToSharePoint(config, "exports", "reports/quarterly.xlsx", [])

if result.success then
    Log("Information", "File uploaded: " + result.fileUrl)
else
    Log("Error", "Upload failed: " + result.error)
end
```

### Upload with metadata

```jyro
var config = {
    siteUrl: "https://contoso.sharepoint.com/sites/Finance",
    folderPath: "/sites/Finance/Shared Documents/Invoices",
    clientId: GetSecret("SP_CLIENT_ID"),
    tenantId: GetSecret("SP_TENANT_ID"),
    certificateBase64: GetSecret("SP_CERTIFICATE_BASE64"),
    listItemType: "SP.Data.Shared_x0020_DocumentsItem"
}

var metadata = [
    { key: "InvoiceNumber", value: "INV-2024-001" },
    { key: "Vendor", value: "Acme Corp" },
    { key: "Amount", value: "1500.00" },
    { key: "Status", value: "Pending Review" }
]

var result = UploadFileToSharePoint(config, "invoices", "pending/invoice.pdf", metadata)

if result.success then
    Log("Information", "Invoice uploaded with metadata: " + result.fileUrl)

    # Move source file to processed
    MoveBlob(GetSecret("BLOB_CONNECTION"), "invoices", "pending/invoice.pdf", "processed/invoice.pdf")
end
```

### Upload with custom filename

```jyro
var config = {
    siteUrl: "https://contoso.sharepoint.com/sites/HR",
    folderPath: "/sites/HR/Shared Documents/Reports",
    clientId: GetSecret("SP_CLIENT_ID"),
    tenantId: GetSecret("SP_TENANT_ID"),
    certificateBase64: GetSecret("SP_CERTIFICATE_BASE64")
}

# Generate a unique filename
var timestamp = FormatDate(Now(), "yyyyMMdd_HHmmss")
var customName = "HR_Report_" + timestamp + ".xlsx"

var result = UploadFileToSharePoint(config, "reports", "generated/hr-data.xlsx", [], customName)

if result.success then
    Log("Information", "Report uploaded as: " + customName)
end
```

### Process and upload multiple files

```jyro
var spConfig = {
    siteUrl: "https://contoso.sharepoint.com/sites/Archive",
    folderPath: "/sites/Archive/Shared Documents/2024/January",
    clientId: GetSecret("SP_CLIENT_ID"),
    tenantId: GetSecret("SP_TENANT_ID"),
    certificateBase64: GetSecret("SP_CERTIFICATE_BASE64"),
    autoCreateColumns: true
}

var blobConnection = GetSecret("BLOB_CONNECTION")
var files = DownloadBlobFiles(blobConnection, "documents", "ready-for-archive/", 50)

var uploaded = 0
var failed = 0

foreach file in files do
    var metadata = [
        { key: "SourceSystem", value: "Iris" },
        { key: "ArchivedDate", value: FormatDate(Now(), "yyyy-MM-dd") },
        { key: "OriginalPath", value: file.blobName }
    ]

    var result = UploadFileToSharePoint(spConfig, "documents", file.blobName, metadata)

    if result.success then
        uploaded = uploaded + 1
        MoveBlob(blobConnection, "documents", file.blobName, "archived/" + file.fileName)
    else
        failed = failed + 1
        Log("Warning", "Failed to upload " + file.fileName + ": " + result.error)
    end
end

Log("Information", "Archive complete: " + uploaded + " uploaded, " + failed + " failed")
```

### Create folder before upload

```jyro
var config = {
    siteUrl: "https://contoso.sharepoint.com/sites/Projects",
    folderPath: "/sites/Projects/Shared Documents",
    clientId: GetSecret("SP_CLIENT_ID"),
    tenantId: GetSecret("SP_TENANT_ID"),
    certificateBase64: GetSecret("SP_CERTIFICATE_BASE64")
}

# Create date-based folder structure
var year = FormatDate(Now(), "yyyy")
var month = FormatDate(Now(), "MM")
var folderPath = "/Shared Documents/" + year + "/" + month

var folderResult = CreateSharePointFolder(config, folderPath)

if folderResult.success then
    # Update config with new folder path
    config.folderPath = "/sites/Projects" + folderPath

    var uploadResult = UploadFileToSharePoint(config, "exports", "report.pdf", [])

    if uploadResult.success then
        Log("Information", "File uploaded to: " + uploadResult.fileUrl)
    end
end
```

## Configuration Tips

### Finding the List Item Type

The `listItemType` varies by document library. Common patterns:
- `SP.Data.Shared_x0020_DocumentsItem` - Default "Shared Documents" library
- `SP.Data.[LibraryName]Item` - Custom libraries (spaces become `_x0020_`)

### Certificate Setup

1. Create a self-signed certificate or use an existing one
2. Export as .pfx with private key
3. Base64 encode the .pfx file
4. Store in Iris Secrets

```jyro
# Store certificate (one-time setup)
var certBase64 = "MIIJ..." # Base64-encoded .pfx content
SetSecret("SP_CERTIFICATE_BASE64", certBase64, "SharePoint app certificate")
```

## Notes

- Files are first downloaded from blob storage to memory, then uploaded to SharePoint
- Large files may timeout or fail - consider file size limits
- Column names with spaces are automatically converted to internal names (e.g., "My Column" → "My_x0020_Column")
- If `autoCreateColumns` is true, new columns are created as single-line text fields
- The function logs warnings but continues if metadata update fails after successful file upload

## Related Functions

- [CreateSharePointFolder](CreateSharePointFolder.md) - Create folders in SharePoint
- [DownloadBlobFiles](../externalblob/DownloadBlobFiles.md) - Download source files
- [MoveBlob](../externalblob/MoveBlob.md) - Move processed files
- [GetSecret](../secrets/GetSecret.md) - Retrieve configuration secrets
