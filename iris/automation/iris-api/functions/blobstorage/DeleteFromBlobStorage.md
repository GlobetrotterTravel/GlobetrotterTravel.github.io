---
layout: default
title: DeleteFromBlobStorage
parent: Iris API functions
has_children: false
has_toc: false
---

# DeleteFromBlobStorage

Deletes a file from Azure Blob Storage for the current tenant.

## Syntax

```jyro
DeleteFromBlobStorage(path, fileName)
```

## Parameters

- **path** (string): Path within the container (e.g., `"exports/old"`)
- **fileName** (string): Name of the file to delete (e.g., `"legacy.xlsx"`)

## Returns

- **boolean**: `true` if deleted, `false` if not found or on error

## Description

Permanently deletes a file from the tenant's blob storage. The operation is idempotent - deleting a non-existent file returns `false` without error.

## Examples

### Basic Delete

```jyro
var deleted = DeleteFromBlobStorage("exports/old", "legacy.xlsx")

if deleted then
    Log("Information", "File deleted successfully")
else
    Log("Information", "File not found or could not be deleted")
end
```

### Delete After Processing

```jyro
# Process an import file
var content = ReadFromBlobStorage("imports", "data.json")

if content != null then
    var jsonString = BytesToString(content)
    var data = ParseJson(jsonString)

    # Process the data...
    foreach record in data do
        # Import logic...
    end

    # Delete the import file after successful processing
    DeleteFromBlobStorage("imports", "data.json")
    Log("Information", "Import file processed and deleted")
end
```

### Cleanup Old Files

```jyro
# Delete files older than 30 days
var files = ListBlobStorage("exports", "*")
var now = Now()
var deletedCount = 0

foreach file in files do
    var fileDate = ParseDate(file.lastModified)
    var ageHours = (now - fileDate) / 3600000  # ms to hours

    if ageHours > 720 then  # 30 days
        var deleted = DeleteFromBlobStorage("exports", file.name)
        if deleted then
            deletedCount = deletedCount + 1
            Log("Information", "Deleted old file: " + file.name)
        end
    end
end

Log("Information", "Cleanup complete. Deleted " + deletedCount + " files")
```

### Move File (Copy + Delete)

```jyro
# Move file from one path to another
var content = ReadFromBlobStorage("exports", "report.xlsx")

if content != null then
    var success = WriteToBlobStorage("archive/2024", "report.xlsx", content)

    if success then
        DeleteFromBlobStorage("exports", "report.xlsx")
        Log("Information", "File moved to archive")
    end
end
```

### Delete with Confirmation

```jyro
# Check if file exists before attempting delete
if BlobStorageExists("temp", "upload.xlsx") then
    var deleted = DeleteFromBlobStorage("temp", "upload.xlsx")
    if deleted then
        Log("Information", "Temporary file cleaned up")
    end
else
    Log("Information", "No temporary file to clean up")
end
```

## Notes

- Deletion is permanent - there is no recycle bin or undo
- Returns `false` for non-existent files (does not throw error)
- Blob storage must be enabled for the tenant (see `IsBlobStorageEnabled`)
- Consider archiving important files before deletion
