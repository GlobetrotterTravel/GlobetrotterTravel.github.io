---
layout: default
title: GetFileHandle
parent: Iris API functions
has_children: false
has_toc: false
---

# GetFileHandle

Retrieves metadata for a file handle by its ID.

## Syntax

```jyro
GetFileHandle(handleId)
```

## Parameters

- **handleId** (string): The unique handle ID returned by `DownloadBlobFiles`

## Returns

- **object**: File handle object with metadata, or `null` if not found

The returned object contains:
- **id** (string): Unique handle ID
- **blobName** (string): Original blob path in Azure storage
- **containerName** (string): Azure container name
- **fileName** (string): File name without path
- **localPath** (string): Local file system path where file is stored
- **sizeBytes** (number): File size in bytes
- **contentType** (string): MIME content type
- **lastModified** (string): ISO 8601 timestamp of last modification in source
- **downloadedAt** (string): ISO 8601 timestamp when file was downloaded

## Description

Retrieves detailed metadata about a downloaded file using its handle ID. This is useful for inspecting file properties before processing, logging file information, or making decisions based on file attributes.

## Examples

### Get file details

```jyro
var connectionString = GetSecret("BLOB_CONNECTION")
var files = DownloadBlobFiles(connectionString, "documents", ["reports/quarterly.xlsx"])

if Length(files) > 0 then
    var handle = GetFileHandle(files[0].id)

    if handle != null then
        Log("Information", "File: " + handle.fileName)
        Log("Information", "Size: " + handle.sizeBytes + " bytes")
        Log("Information", "Type: " + handle.contentType)
        Log("Information", "Modified: " + handle.lastModified)
    end
end
```

### Filter files by size

```jyro
var connectionString = GetSecret("DATA_LAKE_CONNECTION")
var files = DownloadBlobFiles(connectionString, "uploads", "incoming/", 100)

var largeFiles = []
var smallFiles = []
var sizeThreshold = 1048576  # 1 MB

foreach file in files do
    var handle = GetFileHandle(file.id)

    if handle != null then
        if handle.sizeBytes > sizeThreshold then
            largeFiles = Append(largeFiles, handle)
            Log("Warning", "Large file: " + handle.fileName + " (" + handle.sizeBytes + " bytes)")
        else
            smallFiles = Append(smallFiles, handle)
        end
    end
end

Log("Information", "Found " + Length(largeFiles) + " large files and " + Length(smallFiles) + " small files")
```

### Process files by content type

```jyro
var connectionString = GetSecret("IMPORT_CONNECTION")
var files = DownloadBlobFiles(connectionString, "imports", "batch/", 50)

foreach file in files do
    var handle = GetFileHandle(file.id)

    if handle == null then
        continue
    end

    if handle.contentType == "application/json" then
        var content = ReadFileContent(file.id)
        var data = Parse(content)
        Log("Information", "Processing JSON: " + handle.fileName)
        # Process JSON data...

    else if handle.contentType == "text/csv" then
        Log("Information", "Processing CSV: " + handle.fileName)
        # Process CSV data...

    else
        Log("Debug", "Skipping unsupported type: " + handle.contentType)
    end
end
```

### Build file inventory

```jyro
var connectionString = GetSecret("ARCHIVE_CONNECTION")
var files = DownloadBlobFiles(connectionString, "archive", "2024/", 1000)

var inventory = []

foreach file in files do
    var handle = GetFileHandle(file.id)

    if handle != null then
        var item = {
            name: handle.fileName,
            path: handle.blobName,
            size: handle.sizeBytes,
            type: handle.contentType,
            lastModified: handle.lastModified
        }
        inventory = Append(inventory, item)
    end
end

# Create notepad with inventory
Notepad({
    type: "FileInventory",
    generatedAt: Now(),
    fileCount: Length(inventory),
    files: inventory
})

Log("Information", "Created inventory of " + Length(inventory) + " files")
```

### Validate handle before processing

```jyro
# Assume handleId comes from external input
var handleId = Data.fileHandleId

var handle = GetFileHandle(handleId)

if handle == null then
    Log("Warning", "Invalid or expired file handle: " + handleId)
    Data._payload = { error: "File handle not found" }
    Data._statusCode = 404
    return
end

Log("Information", "Processing file: " + handle.fileName)
var content = ReadFileContent(handleId)

# Continue processing...
```

## Notes

- Returns `null` if the handle ID is invalid or has expired
- Handle IDs are GUIDs generated when files are downloaded
- File handles are temporary and cleaned up after script execution
- The `localPath` property shows where the file is stored temporarily on the server

## Related Functions

- [DownloadBlobFiles](../externalblob/DownloadBlobFiles.md) - Download files from external blob storage
- [ReadFileContent](ReadFileContent.md) - Read file text content
