---
layout: default
title: ReadJson
parent: Iris API functions
has_children: false
has_toc: false
---

# ReadJson

Reads and parses a JSON file from base64 encoded data.

## Syntax

```jyro
ReadJson(base64Content)
```

## Parameters

- **base64Content** (string): Base64 encoded JSON file content

## Returns

- **object** or **array**: The parsed JSON data, or `null` on error

## Description

Parses JSON data from a base64-encoded string and returns the native Jyro representation (object or array). Supports any valid JSON structure including nested objects and arrays.

## Examples

```jyro
# Read JSON array
var records = ReadJson(Data.ExternalFile)
if records != null then
    foreach record in records do
        Log("Information", "Processing: " + record.name)
    end
end
```

```jyro
# Read JSON object (configuration)
var config = ReadJson(Data.ExternalFile)
if config != null then
    Log("Information", "Setting: " + config.setting)
    Log("Information", "Enabled: " + config.enabled)
end
```

```jyro
# Import organizations from JSON
var data = ReadJson(Data.ExternalFile)

if data == null then
    Log("Error", "Invalid JSON file")
    return
end

foreach org in data.organizations do
    var orgId = CreateOrganization(org.name, org.description, "Replace")

    if orgId != null then
        Log("Information", "Created organization: " + org.name)

        # Set facets if defined in JSON
        if org.facets != null then
            foreach facetData in org.facets do
                var facetDef = GetFacetDefinitionByName(facetData.name)
                if facetDef != null then
                    SetOrganizationFacetInstance(orgId, facetDef.id, facetData.values)
                end
            end
        end
    end
end
```

```jyro
# Process nested JSON structure
var data = ReadJson(Data.ExternalFile)

if data.metadata != null then
    Log("Information", "File version: " + data.metadata.version)
    Log("Information", "Generated: " + data.metadata.timestamp)
end

if data.items != null then
    Log("Information", "Processing " + Length(data.items) + " items")
    foreach item in data.items do
        # Access nested properties
        if item.details != null then
            Log("Information", "Item: " + item.name + " - " + item.details.description)
        end
    end
end
```

```jyro
# Validate JSON structure before processing
var data = ReadJson(Data.ExternalFile)

if data == null then
    Log("Error", "Failed to parse JSON")
    CancelAction("Invalid JSON file")
    return
end

# Check for required properties
if data.version == null then
    Log("Error", "JSON missing required 'version' property")
    CancelAction("Invalid JSON format: missing version")
    return
end

if data.records == null or Length(data.records) == 0 then
    Log("Warning", "JSON contains no records")
    return
end

Log("Information", "JSON validated: version " + data.version + " with " + Length(data.records) + " records")
```

## Supported JSON Types

| JSON Type | Jyro Type |
|-----------|-----------|
| object `{}` | JyroObject |
| array `[]` | JyroArray |
| string `"text"` | JyroString |
| number `123.45` | JyroNumber |
| boolean `true/false` | JyroBoolean |
| null | JyroNull |

## Error Handling

The function returns `null` on error, including:
- Invalid base64 encoding
- Malformed JSON syntax
- Empty content

Always check for null before accessing properties.

## Related Functions

- [ReadCsv](ReadCsv) - Read CSV files
- [ReadExcel](ReadExcel) - Read Excel (.xlsx) files
- [ReadXml](ReadXml) - Read XML files
