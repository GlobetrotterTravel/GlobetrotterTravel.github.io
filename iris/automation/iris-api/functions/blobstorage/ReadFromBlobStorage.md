---
layout: default
title: ReadFromBlobStorage
parent: Iris API functions
has_children: false
has_toc: false
---

# ReadFromBlobStorage

Downloads content from Azure Blob Storage for the current tenant.

## Syntax

```jyro
ReadFromBlobStorage(path, fileName)
```

## Parameters

- **path** (string): Path within the container (e.g., `"exports/reports"`)
- **fileName** (string): Name of the file (e.g., `"data.json"`)

## Returns

- **array**: File content as a byte array, or `null` if not found or on error

## Description

Downloads a file from the tenant's Azure Blob Storage account. The returned byte array can be:
- Passed directly to `WriteToBlobStorage` for copying files
- Converted to string for JSON/text files
- Processed as binary data

## Examples

### Read JSON File

```jyro
var content = ReadFromBlobStorage("config", "settings.json")

if content != null then
    # Convert byte array to string (for JSON/text files)
    var jsonString = BytesToString(content)
    var settings = ParseJson(jsonString)
    Log("Information", "Loaded settings: " + settings.version)
else
    Log("Warning", "Settings file not found")
end
```

### Check File Existence Before Reading

```jyro
if BlobStorageExists("exports", "report.xlsx") then
    var content = ReadFromBlobStorage("exports", "report.xlsx")
    Log("Information", "Downloaded file with " + content.length + " bytes")
else
    Log("Information", "File does not exist")
end
```

### Copy File to New Location

```jyro
# Read from source
var content = ReadFromBlobStorage("exports/2024/01", "report.xlsx")

if content != null then
    # Write to destination
    var success = WriteToBlobStorage("archive", "report_archived.xlsx", content)
    if success then
        Log("Information", "File copied successfully")
    end
end
```

### Process Multiple Files

```jyro
# List and process all JSON files
var files = ListBlobStorage("data", "*.json")

foreach file in files do
    var content = ReadFromBlobStorage("data", file.name)
    if content != null then
        var jsonString = BytesToString(content)
        var data = ParseJson(jsonString)
        # Process data...
        Log("Information", "Processed: " + file.name)
    end
end
```

## Notes

- Returns `null` if the file doesn't exist or blob storage is not enabled
- For large files, consider using `GetBlobStorageSasUrl` instead to generate a download link
- Byte arrays can be large - avoid storing in notepads or logging
- Blob storage must be enabled for the tenant (see `IsBlobStorageEnabled`)
