---
layout: default
title: ReadCsv
parent: Iris API functions
has_children: false
has_toc: false
---

# ReadCsv

Reads and parses a CSV file from base64 encoded data.

## Syntax

```jyro
ReadCsv(base64Content)
ReadCsv(base64Content, delimiter)
ReadCsv(base64Content, delimiter, hasHeader)
```

## Parameters

- **base64Content** (string): Base64 encoded CSV file content
- **delimiter** (string, optional): Column delimiter (default: ",")
- **hasHeader** (boolean, optional): Whether the first row is a header row (default: true)

## Returns

- **array**: Array of objects where each object represents a row with column names as keys, or empty array on error

## Description

Parses CSV data from a base64-encoded string and returns an array of row objects. When `hasHeader` is true, the first row's values become the property names for each row object. When false, columns are named Column1, Column2, etc.

Header names are automatically normalized to be valid property names (spaces become underscores, special characters removed).

## Examples

```jyro
# Read CSV with default settings (comma delimiter, has header)
var rows = ReadCsv(Data.ExternalFile)
foreach row in rows do
    Log("Information", row.FirstName + " " + row.LastName)
end
```

```jyro
# Read CSV with semicolon delimiter
var rows = ReadCsv(Data.ExternalFile, ";")
foreach row in rows do
    Log("Information", "Name: " + row.Name + ", Email: " + row.Email)
end
```

```jyro
# Read CSV without header (columns named Column1, Column2, etc.)
var rows = ReadCsv(Data.ExternalFile, ",", false)
foreach row in rows do
    Log("Information", "First column: " + row.Column1)
    Log("Information", "Second column: " + row.Column2)
end
```

```jyro
# Process CSV data to create persons
var rows = ReadCsv(Data.ExternalFile)
var created = 0
var failed = 0

foreach row in rows do
    var personId = CreatePerson(row.FirstName, row.LastName, row.Email)
    if personId != null then
        created = created + 1

        # Set additional facets if available
        if row.Department != null and row.Department != "" then
            var deptFacetId = GetFacetDefinitionByName("Department")
            if deptFacetId != null then
                SetPersonFacetInstance(personId, deptFacetId.id, [row.Department])
            end
        end
    else
        failed = failed + 1
        Log("Warning", "Failed to create person: " + row.Email)
    end
end

Log("Information", "Import complete: " + created + " created, " + failed + " failed")
```

```jyro
# Validate CSV data before processing
var rows = ReadCsv(Data.ExternalFile)

if Length(rows) == 0 then
    Log("Error", "CSV file is empty or invalid")
    CancelAction("Empty or invalid CSV file")
    return
end

# Check required columns exist
var firstRow = rows[0]
if firstRow.Email == null then
    Log("Error", "CSV is missing required 'Email' column")
    CancelAction("Missing required Email column")
    return
end

Log("Information", "CSV validated: " + Length(rows) + " rows to process")
```

## Header Normalization

CSV column headers are normalized to valid Jyro property names:

| Original Header | Normalized Name |
|-----------------|-----------------|
| `First Name` | `First_Name` |
| `email-address` | `email_address` |
| `Phone (Work)` | `Phone_Work` |
| `123Column` | `_123Column` |

## Error Handling

The function returns an empty array on error, including:
- Invalid base64 encoding
- Malformed CSV data
- Empty content

Always check the length of the returned array before processing.

## Related Functions

- [ReadExcel](ReadExcel.md) - Read Excel (.xlsx) files
- [ReadJson](ReadJson.md) - Read JSON files
- [ReadXml](ReadXml.md) - Read XML files
