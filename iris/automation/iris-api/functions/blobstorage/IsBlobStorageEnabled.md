---
layout: default
title: IsBlobStorageEnabled
parent: Iris API functions
has_children: false
has_toc: false
---

# IsBlobStorageEnabled

Checks if Azure Blob Storage is configured and enabled for the current tenant.

## Syntax

```jyro
IsBlobStorageEnabled()
```

## Parameters

None.

## Returns

- **boolean**: `true` if blob storage is enabled and configured, `false` otherwise

## Description

Checks whether blob storage is available for use in the current tenant context. This function verifies that:
- The `blobstorage.enabled` property is set to `"true"`
- The `blobstorage.connectionstring` secret is configured

Use this function before calling other blob storage functions to gracefully handle tenants that don't have blob storage configured.

## Configuration

To enable blob storage for a tenant, configure the following:

### Property Values

| Property Name | Type | Default | Description |
|--------------|------|---------|-------------|
| `blobstorage.enabled` | String | `"false"` | Set to `"true"` to enable blob storage |
| `blobstorage.container.name` | String | `"iris-exports"` | Azure Blob container name |
| `blobstorage.default.path` | String | `"exports"` | Default path prefix for files |
| `blobstorage.maxfilesize.mb` | String | `"100"` | Maximum file size in MB |

### Secrets

| Secret Name | Description |
|------------|-------------|
| `blobstorage.connectionstring` | Azure Blob Storage connection string |

## Examples

### Basic Check

```jyro
if IsBlobStorageEnabled() then
    Log("Information", "Blob storage is available")
else
    Log("Warning", "Blob storage is not configured for this tenant")
end
```

### Conditional Storage Strategy

```jyro
# Generate Excel file
var worksheets = [{
    name: "Report",
    data: reportData
}]

if IsBlobStorageEnabled() then
    # Persist to blob for long-term storage
    var excelBytes = GenerateExcelBytes(worksheets)
    WriteToBlobStorage("exports", "report.xlsx", excelBytes)
    var downloadUrl = GetBlobStorageSasUrl("exports", "report.xlsx", 1440)

    Data._payload = {
        downloadUrl: downloadUrl,
        persistent: true,
        expiresIn: "24 hours (link only - file persists)"
    }
else
    # Fall back to temporary in-memory storage
    var tempUrl = GenerateExcel(worksheets, "report.xlsx")

    Data._payload = {
        downloadUrl: tempUrl,
        persistent: false,
        expiresIn: "1 hour"
    }
end
Data._statusCode = 200
```

## Notes

- Returns `false` if any required configuration is missing
- The function is lightweight and can be called frequently
- Each tenant must configure their own Azure Blob Storage account for isolation
