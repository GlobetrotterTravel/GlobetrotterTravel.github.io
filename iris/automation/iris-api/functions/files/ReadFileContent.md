---
layout: default
title: ReadFileContent
parent: Iris API functions
has_children: false
has_toc: false
---

# ReadFileContent

Reads the text content of a file that was downloaded using `DownloadBlobFiles`.

## Syntax

```jyro
ReadFileContent(handleId)
```

## Parameters

- **handleId** (string): The unique handle ID returned by `DownloadBlobFiles`

## Returns

- **string**: The text content of the file, or `null` if the handle is not found or the file cannot be read

## Description

Retrieves the content of a downloaded file using its handle ID. Files must first be downloaded using `DownloadBlobFiles`, which returns handle objects containing the `id` property needed for this function.

This function reads the entire file as text (UTF-8). For binary files or structured data formats, consider using the specialized read functions (`ReadCsv`, `ReadExcel`, `ReadJson`, `ReadXml`) with base64-encoded content instead.

## Examples

### Read a downloaded file

```jyro
var connectionString = GetSecret("EXTERNAL_BLOB_CONNECTION")

# Download files from external blob storage
var files = DownloadBlobFiles(connectionString, "data-container", ["config/settings.json"])

if Length(files) > 0 then
    var content = ReadFileContent(files[0].id)

    if content != null then
        Log("Information", "File content length: " + Length(content))
        # Parse as JSON
        var settings = Parse(content)
        Log("Information", "Setting value: " + settings.someProperty)
    else
        Log("Error", "Could not read file content")
    end
else
    Log("Warning", "No files downloaded")
end
```

### Process multiple downloaded files

```jyro
var connectionString = GetSecret("DATA_LAKE_CONNECTION")

# Download all CSV files from a folder
var handles = DownloadBlobFiles(connectionString, "reports", "daily/", 50)

Log("Information", "Downloaded " + Length(handles) + " files")

foreach handle in handles do
    var content = ReadFileContent(handle.id)

    if content != null then
        Log("Information", "Processing: " + handle.fileName + " (" + Length(content) + " chars)")

        # For CSV files, you might want to parse the content
        # Note: For proper CSV parsing, use ReadCsv with base64 content
        var lines = Split(content, "\n")
        Log("Debug", "File has " + Length(lines) + " lines")
    else
        Log("Warning", "Could not read: " + handle.fileName)
    end
end
```

### Read and process text file

```jyro
var connectionString = GetSecret("IMPORT_STORAGE_CONNECTION")

var files = DownloadBlobFiles(connectionString, "imports", ["batch/emails.txt"])

if Length(files) > 0 then
    var content = ReadFileContent(files[0].id)

    if content != null then
        # Split by newlines to get individual email addresses
        var emails = Split(content, "\n")
        var validCount = 0

        foreach email in emails do
            var trimmed = Trim(email)
            if trimmed != "" and Contains(trimmed, "@") then
                validCount = validCount + 1
                Log("Debug", "Valid email: " + trimmed)
            end
        end

        Log("Information", "Found " + validCount + " valid email addresses")
    end
end
```

### Error handling

```jyro
var connectionString = GetSecret("BLOB_CONNECTION")
var files = DownloadBlobFiles(connectionString, "container", ["path/to/file.txt"])

if Length(files) == 0 then
    Log("Error", "File not found or download failed")
    Data._payload = { error: "File not found" }
    Data._statusCode = 404
    return
end

var content = ReadFileContent(files[0].id)

if content == null then
    Log("Error", "Could not read file content - file may be binary")
    Data._payload = { error: "Could not read file" }
    Data._statusCode = 500
    return
end

Data._payload = { content: content, size: Length(content) }
```

## Notes

- The file must be downloaded first using `DownloadBlobFiles`
- Returns `null` if the handle ID is invalid, expired, or the file cannot be read
- Reads files as UTF-8 text - binary files may not be read correctly
- Handle IDs are GUIDs and are case-insensitive
- Downloaded files are temporary and cleaned up after script execution

## Related Functions

- [DownloadBlobFiles](../externalblob/DownloadBlobFiles) - Download files from external blob storage
- [GetFileHandle](GetFileHandle) - Get file handle metadata
- [ReadCsv](../data/ReadCsv) - Parse CSV content
- [ReadJson](../data/ReadJson) - Parse JSON content
- [ReadExcel](../data/ReadExcel) - Parse Excel files
