---
layout: default
title: ReadExcel
parent: Iris API functions
has_children: false
has_toc: false
---

# ReadExcel

Reads and parses an Excel file (.xlsx) from base64 encoded data.

## Syntax

```jyro
ReadExcel(base64Content)
ReadExcel(base64Content, sheetName)
ReadExcel(base64Content, sheetName, hasHeader)
```

## Parameters

- **base64Content** (string): Base64 encoded Excel file content (.xlsx format)
- **sheetName** (string, optional): Name of the worksheet to read (default: first sheet)
- **hasHeader** (boolean, optional): Whether the first row is a header row (default: true)

## Returns

- **array**: Array of objects where each object represents a row with column names as keys, or empty array on error

## Description

Parses Excel data from a base64-encoded .xlsx file and returns an array of row objects. When `hasHeader` is true, the first row's values become the property names for each row object. When false, columns are named Column1, Column2, etc.

The function preserves data types from Excel: numbers stay as numbers, dates are converted to ISO 8601 strings, and booleans stay as booleans.

## Examples

```jyro
# Read Excel with default settings (first sheet, has header)
var rows = ReadExcel(Data.ExternalFile)
foreach row in rows do
    Log("Information", row.FirstName + " " + row.LastName)
end
```

```jyro
# Read a specific sheet
var rows = ReadExcel(Data.ExternalFile, "Employees")
Log("Information", "Read " + Length(rows) + " rows from Employees sheet")
```

```jyro
# Read without header (columns named Column1, Column2, etc.)
var rows = ReadExcel(Data.ExternalFile, null, false)
foreach row in rows do
    Log("Information", "A: " + row.Column1 + ", B: " + row.Column2)
end
```

```jyro
# Import organizations from Excel
var rows = ReadExcel(Data.ExternalFile)

if Length(rows) == 0 then
    Log("Warning", "Excel file is empty")
    return
end

var imported = 0
foreach row in rows do
    # Skip empty rows
    if row.Name == null or row.Name == "" then
        continue
    end

    var orgId = CreateOrganization(row.Name, row.Description, "Replace")
    if orgId != null then
        imported = imported + 1

        # Set facets from Excel columns
        if row.Industry != null and row.Industry != "" then
            var industryFacet = GetFacetDefinitionByName("Industry")
            if industryFacet != null then
                SetOrganizationFacetInstance(orgId, industryFacet.id, [row.Industry])
            end
        end
    end
end

Log("Information", "Imported " + imported + " organizations from Excel")
```

```jyro
# Process Excel with type-aware data
var rows = ReadExcel(Data.ExternalFile, "Products")

foreach row in rows do
    # Numbers stay as numbers
    var totalValue = row.Quantity * row.UnitPrice
    Log("Information", row.ProductName + ": " + totalValue)

    # Dates are ISO 8601 strings
    Log("Information", "Created: " + row.CreatedDate)

    # Booleans work directly
    if row.IsActive then
        Log("Information", row.ProductName + " is active")
    end
end
```

```jyro
# Handle missing sheet gracefully
var rows = ReadExcel(Data.ExternalFile, "NonExistentSheet")

if Length(rows) == 0 then
    Log("Warning", "Sheet not found or empty - trying first sheet")
    rows = ReadExcel(Data.ExternalFile)
end

if Length(rows) > 0 then
    Log("Information", "Successfully read " + Length(rows) + " rows")
end
```

## Data Type Handling

| Excel Type | Jyro Type | Example |
|------------|-----------|---------|
| Number | JyroNumber | `123.45` |
| Text | JyroString | `"Hello"` |
| Boolean | JyroBoolean | `true` |
| Date/Time | JyroString (ISO 8601) | `"2024-01-15T10:30:00"` |
| Empty | JyroString | `""` |

## Header Normalization

Column headers are normalized to valid Jyro property names (same as CSV):

| Original Header | Normalized Name |
|-----------------|-----------------|
| `First Name` | `First_Name` |
| `email-address` | `email_address` |
| `123Column` | `_123Column` |

## Error Handling

The function returns an empty array on error, including:
- Invalid base64 encoding
- Invalid Excel format (not .xlsx)
- Sheet not found
- Empty worksheet

## Related Functions

- [ReadCsv](ReadCsv.md) - Read CSV files
- [ReadJson](ReadJson.md) - Read JSON files
- [ReadXml](ReadXml.md) - Read XML files
