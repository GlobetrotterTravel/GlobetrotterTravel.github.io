---
layout: default
title: DownloadBlobFiles
parent: Iris API functions
has_children: false
has_toc: false
---

# DownloadBlobFiles

Downloads files from any Azure Blob Storage account and returns file handles for processing.

## Syntax

```jyro
DownloadBlobFiles(connectionString, containerName, blobPaths)
DownloadBlobFiles(connectionString, containerName, prefix, maxFiles)
```

## Parameters

- **connectionString** (string): Azure Blob Storage connection string (typically from a secret)
- **containerName** (string): The Azure Blob Storage container name
- **blobPaths** (array): Array of specific blob paths to download
- **prefix** (string): Prefix string to list and download matching blobs
- **maxFiles** (number, optional): Maximum files to download when using prefix mode (default: 100, max: 1000)

## Returns

- **array**: Array of file handle objects, or empty array on error

Each file handle object contains:
- **id** (string): Unique handle ID for referencing the file
- **blobName** (string): Original blob path in Azure
- **containerName** (string): Container name
- **fileName** (string): File name without path
- **localPath** (string): Local file system path where file is stored
- **sizeBytes** (number): File size in bytes
- **contentType** (string): MIME content type
- **lastModified** (string): ISO 8601 timestamp of last modification
- **downloadedAt** (string): ISO 8601 timestamp when downloaded

## Description

Downloads files from an external Azure Blob Storage account (not the tenant's configured storage). This function supports two modes:

1. **Specific paths**: Provide an array of exact blob paths to download specific files
2. **Prefix listing**: Provide a prefix string to list and download all matching blobs

Downloaded files are stored temporarily and can be accessed using `ReadFileContent` or `GetFileHandle`. Files are automatically cleaned up after script execution.

## Examples

### Download specific files

```jyro
var connectionString = GetSecret("EXTERNAL_STORAGE_CONNECTION")

var files = DownloadBlobFiles(connectionString, "data-container",
    ["folder/file1.csv", "folder/file2.csv", "config.json"])

foreach file in files do
    Log("Information", "Downloaded: " + file.fileName + " (" + file.sizeBytes + " bytes)")

    var content = ReadFileContent(file.id)
    if content != null then
        # Process content...
    end
end
```

### Download files matching a prefix

```jyro
var connectionString = GetSecret("DATA_LAKE_CONNECTION")

# Download all files in the "2024/january/" folder (up to 50)
var reports = DownloadBlobFiles(connectionString, "reports", "2024/january/", 50)

Log("Information", "Downloaded " + Length(reports) + " report files")

foreach report in reports do
    Log("Debug", "File: " + report.blobName + " - Type: " + report.contentType)
end
```

### Handle empty results

```jyro
var connectionString = GetSecret("ARCHIVE_CONNECTION")

var files = DownloadBlobFiles(connectionString, "archive", "old-data/")

if Length(files) == 0 then
    Log("Warning", "No files found matching prefix")
    Data._payload = { found: false, message: "No files in archive" }
    return
end

Log("Information", "Found " + Length(files) + " archived files")
```

### Process CSV files from external storage

```jyro
var connectionString = GetSecret("IMPORT_BLOB_CONNECTION")

var files = DownloadBlobFiles(connectionString, "imports", ["daily/customers.csv"])

if Length(files) == 0 then
    Log("Error", "Customer import file not found")
    return
end

var csvContent = ReadFileContent(files[0].id)
if csvContent == null then
    Log("Error", "Could not read CSV file")
    return
end

# Convert to base64 for ReadCsv function
var base64Content = ToBase64(csvContent)
var rows = ReadCsv(base64Content)

Log("Information", "Processing " + (Length(rows) - 1) + " customers")

foreach row in Slice(rows, 1) do
    # Process each row...
end
```

### Download and move processed files

```jyro
var connectionString = GetSecret("PROCESSING_STORAGE_CONNECTION")

# Download pending files
var files = DownloadBlobFiles(connectionString, "invoices", "pending/", 100)

foreach file in files do
    var content = ReadFileContent(file.id)

    if content != null then
        # Process the file...
        Log("Information", "Processed: " + file.fileName)

        # Move to processed folder
        MoveBlob(connectionString, "invoices", file.blobName, "processed/" + file.fileName)
    end
end
```

### Filter by file type

```jyro
var connectionString = GetSecret("DOCUMENT_STORAGE_CONNECTION")

var files = DownloadBlobFiles(connectionString, "documents", "inbox/", 200)

var pdfFiles = []
var excelFiles = []

foreach file in files do
    if EndsWith(file.fileName, ".pdf") then
        pdfFiles = Append(pdfFiles, file)
    else if EndsWith(file.fileName, ".xlsx") then
        excelFiles = Append(excelFiles, file)
    end
end

Log("Information", "Found " + Length(pdfFiles) + " PDFs and " + Length(excelFiles) + " Excel files")
```

## Notes

- Connection string should be stored as a secret for security
- Maximum 1000 files can be downloaded in a single call
- Files are downloaded to temporary storage and cleaned up after script execution
- Use `ReadFileContent` to read text files, or the specialized read functions for structured data
- This function is for external blob storage; for tenant blob storage, use `ReadFromBlobStorage`

## Related Functions

- [ReadFileContent](../files/ReadFileContent.md) - Read downloaded file content
- [GetFileHandle](../files/GetFileHandle.md) - Get file handle metadata
- [MoveBlob](MoveBlob.md) - Move blobs within storage
- [ReadFromBlobStorage](../blobstorage/ReadFromBlobStorage.md) - Read from tenant blob storage
- [GetSecret](../secrets/GetSecret.md) - Retrieve connection string from secrets
