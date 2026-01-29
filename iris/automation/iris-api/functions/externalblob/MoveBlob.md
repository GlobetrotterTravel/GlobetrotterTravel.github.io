---
layout: default
title: MoveBlob
parent: Iris API functions
has_children: false
has_toc: false
---

# MoveBlob

Moves a blob to a new location within the same Azure Blob Storage container.

## Syntax

```jyro
MoveBlob(connectionString, containerName, sourcePath, destinationPath)
```

## Parameters

- **connectionString** (string): Azure Blob Storage connection string (typically from a secret)
- **containerName** (string): The Azure Blob Storage container name
- **sourcePath** (string): Current blob path (e.g., `"pending/invoice.pdf"`)
- **destinationPath** (string): New blob path (e.g., `"processed/invoice.pdf"`)

## Returns

- **object**: Result object with properties:
  - **success** (boolean): Whether the move succeeded
  - **destinationPath** (string): The new blob path (on success)
  - **error** (string): Error message (on failure)

## Description

Moves a blob from one location to another within the same container. Internally, this performs a copy operation followed by deletion of the original blob, ensuring atomicity.

This is commonly used for workflow processing where files are moved between folders (e.g., from "pending" to "processed" or "failed").

## Examples

### Move a single file

```jyro
var connectionString = GetSecret("BLOB_STORAGE_CONNECTION")

var result = MoveBlob(connectionString, "invoices", "pending/invoice-001.pdf", "processed/invoice-001.pdf")

if result.success then
    Log("Information", "File moved to: " + result.destinationPath)
else
    Log("Error", "Move failed: " + result.error)
end
```

### Process and move files

```jyro
var connectionString = GetSecret("PROCESSING_STORAGE_CONNECTION")

# Download files from pending folder
var files = DownloadBlobFiles(connectionString, "documents", "pending/", 100)

foreach file in files do
    var success = false

    # Try to process the file
    var content = ReadFileContent(file.id)
    if content != null then
        # Process content...
        success = true
        Log("Information", "Processed: " + file.fileName)
    else
        Log("Warning", "Could not read: " + file.fileName)
    end

    # Move to appropriate folder based on result
    if success then
        var result = MoveBlob(connectionString, "documents", file.blobName, "processed/" + file.fileName)
    else
        var result = MoveBlob(connectionString, "documents", file.blobName, "failed/" + file.fileName)
    end
end
```

### Archive processed files with date folders

```jyro
var connectionString = GetSecret("ARCHIVE_CONNECTION")

var files = DownloadBlobFiles(connectionString, "reports", "completed/", 50)

var year = FormatDate(Now(), "yyyy")
var month = FormatDate(Now(), "MM")

foreach file in files do
    var archivePath = "archive/" + year + "/" + month + "/" + file.fileName

    var result = MoveBlob(connectionString, "reports", file.blobName, archivePath)

    if result.success then
        Log("Information", "Archived: " + file.fileName + " -> " + archivePath)
    else
        Log("Error", "Failed to archive " + file.fileName + ": " + result.error)
    end
end
```

### Move with error handling

```jyro
var connectionString = GetSecret("BLOB_CONNECTION")

var sourcePath = "incoming/data.csv"
var destPath = "processing/data.csv"

var result = MoveBlob(connectionString, "files", sourcePath, destPath)

if not result.success then
    if Contains(result.error, "does not exist") then
        Log("Warning", "Source file not found: " + sourcePath)
    else
        Log("Error", "Unexpected error: " + result.error)
    end

    Data._payload = { error: result.error }
    Data._statusCode = 500
    return
end

Log("Information", "File ready for processing at: " + result.destinationPath)
```

### Rename a file

```jyro
var connectionString = GetSecret("STORAGE_CONNECTION")

# Rename by moving to same folder with different name
var oldName = "reports/quarterly_draft.xlsx"
var newName = "reports/quarterly_final.xlsx"

var result = MoveBlob(connectionString, "documents", oldName, newName)

if result.success then
    Log("Information", "File renamed successfully")
end
```

### Batch move with logging

```jyro
var connectionString = GetSecret("WORKFLOW_STORAGE_CONNECTION")

var files = DownloadBlobFiles(connectionString, "workflow", "review/approved/", 100)

var moved = 0
var failed = 0

foreach file in files do
    var result = MoveBlob(connectionString, "workflow",
        file.blobName,
        "published/" + file.fileName)

    if result.success then
        moved = moved + 1
    else
        failed = failed + 1
        Log("Warning", "Failed to move " + file.fileName + ": " + result.error)
    end
end

Log("Information", "Batch move complete: " + moved + " moved, " + failed + " failed")

# Record results
Notepad({
    operation: "BatchMove",
    timestamp: Now(),
    movedCount: moved,
    failedCount: failed
})
```

## Error Scenarios

Common errors include:

- **Source blob does not exist**: The source path is invalid or file was already moved
- **Invalid connection string format**: The connection string is malformed
- **Source and destination paths cannot be the same**: No-op is not allowed
- **Permission denied**: The connection string doesn't have write access

## Notes

- Both source and destination must be in the same container
- The operation is atomic (copy + delete)
- If the destination already exists, it will be overwritten
- Snapshots are deleted along with the source blob
- Connection strings should be stored securely as secrets

## Related Functions

- [DownloadBlobFiles](DownloadBlobFiles) - Download files from external blob storage
- [ReadFileContent](../files/ReadFileContent) - Read downloaded file content
- [GetSecret](../secrets/GetSecret) - Retrieve connection string from secrets
- [DeleteFromBlobStorage](../blobstorage/DeleteFromBlobStorage) - Delete from tenant blob storage
