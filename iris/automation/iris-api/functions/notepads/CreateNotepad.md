---
layout: default
title: CreateNotepad
parent: Iris API functions
has_children: false
has_toc: false
---

# CreateNotepad

Creates a Notepad document to persist script output data. Notepads are automatically linked to the script execution and can be retrieved later for debugging, auditing, or further processing.

## Syntax

```jyro
CreateNotepad(content)
```

## Parameters

- **content** (any): Any value to store - objects, arrays, strings, numbers, etc. The content is automatically serialized to JSON.

## Returns

- **string**: The notepad ID (GUID), or null on failure

## Description

Creates a persistent Notepad document containing the provided content. The notepad is automatically:
- Associated with the current script execution (ExecutionId)
- Linked to the source script (if applicable)
- Stored in the tenant's Cosmos DB container
- Available in the Scripting Dashboard execution details

Notepads are particularly useful for:
- **Debugging**: Capture intermediate state during script execution
- **Auditing**: Record decisions, processed data, or external API responses
- **Error Diagnostics**: Persist data at failure points for troubleshooting
- **Data Export**: Generate reports or export datasets

The content is serialized with camelCase property names for consistency with JavaScript conventions.

## Examples

### Basic Usage

```jyro
# Store a simple object
var reportId = CreateNotepad({
    reportType: "Daily Summary",
    generatedAt: Now(),
    totalRecords: 42
})

Log("Information", "Created report notepad: " + reportId)
```

### Capturing Processing Results

```jyro
# Process organizations and capture results
var orgs = GetAllOrganizations()
var results = []

foreach org in orgs do
    var facetData = GetOrganizationFacetInstances(org.id)
    results.add({
        id: org.id,
        name: org.name,
        facetCount: facetData.length
    })
end

# Save the results for later review
var notepadId = CreateNotepad({
    processedAt: Now(),
    organizationCount: orgs.length,
    results: results
})

Log("Information", "Saved processing results to notepad: " + notepadId)
```

### Error Diagnostics

```jyro
# Capture data before potential failure point
var externalData = InvokeRestMethod("GET", "https://api.example.com/data", {}, {})

# Save the response for debugging if something goes wrong
var debugNotepadId = CreateNotepad({
    timestamp: Now(),
    apiResponse: externalData,
    context: Data.person
})

# Process the data...
if externalData.status != "success" then
    Log("Error", "API call failed. Debug notepad: " + debugNotepadId)
    CancelAction("External API returned error status")
end
```

### Data Export

```jyro
# Generate a CSV-style export as a notepad
var persons = GetAllPersons()
var exportData = []

foreach person in persons do
    exportData.add({
        email: person.emailAddress,
        firstName: person.firstName,
        lastName: person.lastName,
        createdAt: person.createdAt
    })
end

var exportId = CreateNotepad({
    exportType: "PersonList",
    exportDate: Now(),
    recordCount: exportData.length,
    data: exportData
})

# Notify admin about the export
SendNotification(
    "admin@example.com",
    "Person Export Ready",
    "Export contains " + exportData.length + " records. Notepad ID: " + exportId,
    "Success",
    "/scripting-dashboard"
)
```

## Notes

- Notepads are created asynchronously for performance - the function returns immediately with the ID
- The notepad ID is a GUID that can be used with `GetNotepadContent()` to retrieve the data
- Content is stored with a 30-day TTL by default (configurable)
- Large content is supported but may impact performance
- Notepads created during a script execution appear in the Scripting Dashboard details panel
