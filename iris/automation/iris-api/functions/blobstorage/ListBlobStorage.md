---
layout: default
title: ListBlobStorage
parent: Iris API functions
has_children: false
has_toc: false
---

# ListBlobStorage

Lists files in Azure Blob Storage for the current tenant.

## Syntax

```jyro
ListBlobStorage(path)
ListBlobStorage(path, prefix)
```

## Parameters

- **path** (string): Path within the container to list (e.g., `"exports"`, `"exports/2024/01"`)
- **prefix** (string, optional): Filter prefix - supports wildcards like `"*.xlsx"` or `"report_"`

## Returns

- **array**: Array of file info objects, or empty array on error

Each file info object contains:
```jyro
{
    name: "report.xlsx",           # File name only
    path: "exports/report.xlsx",   # Full path within container
    sizeBytes: 12345,              # File size in bytes
    lastModified: "2024-01-15T14:30:22Z",  # ISO 8601 timestamp
    contentType: "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
}
```

## Description

Lists all files within a specified path in the tenant's blob storage. Supports optional prefix filtering with wildcards.

## Examples

### List All Files in a Path

```jyro
var files = ListBlobStorage("exports")

Log("Information", "Found " + files.length + " files")

foreach file in files do
    Log("Information", file.name + " - " + file.sizeBytes + " bytes")
end
```

### Filter by Extension

```jyro
# List only Excel files
var excelFiles = ListBlobStorage("exports", "*.xlsx")

foreach file in excelFiles do
    Log("Information", "Excel file: " + file.name)
end
```

### Filter by Prefix

```jyro
# List files starting with "report_"
var reportFiles = ListBlobStorage("exports", "report_")

foreach file in reportFiles do
    Log("Information", "Report: " + file.name + ", Size: " + file.sizeBytes)
end
```

### Browse by Month

```jyro
# List exports from a specific month
var month = FormatDate(Now(), "yyyy/MM")
var files = ListBlobStorage("exports/" + month, "*.xlsx")

Data._payload = {
    month: month,
    fileCount: files.length,
    files: files
}
Data._statusCode = 200
```

### Find Recent Files

```jyro
var files = ListBlobStorage("exports", "*")
var recentFiles = []

foreach file in files do
    # Check if modified in last 24 hours
    var fileDate = ParseDate(file.lastModified)
    var hoursSinceModified = (Now() - fileDate) / 3600000

    if hoursSinceModified < 24 then
        recentFiles.add(file)
    end
end

Log("Information", "Found " + recentFiles.length + " files from last 24 hours")
```

### Build File Browser Endpoint

```jyro
# Dynamic endpoint: GET /api/dynamic/v1/files
var path = Data.request.query.path ?? "exports"
var filter = Data.request.query.filter ?? "*"

var files = ListBlobStorage(path, filter)

# Add download URLs
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
    filter: filter,
    fileCount: filesWithUrls.length,
    files: filesWithUrls
}
Data._statusCode = 200
```

## Notes

- Returns an empty array if the path doesn't exist or blob storage is not enabled
- Wildcard patterns use regex matching internally
- Results are not sorted - sort client-side if needed
- For large directories, consider using a specific prefix to reduce results
