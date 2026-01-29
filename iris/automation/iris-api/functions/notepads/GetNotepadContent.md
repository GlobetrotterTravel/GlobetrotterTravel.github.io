---
layout: default
title: GetNotepadContent
parent: Iris API functions
has_children: false
has_toc: false
---

# GetNotepadContent

Retrieves the content of a specific Notepad document by its ID.

## Syntax

```jyro
GetNotepadContent(notepadId)
```

## Parameters

- **notepadId** (string): The notepad ID (GUID) to retrieve

## Returns

- **any**: The notepad content as a parsed JSON object, or null if not found

## Description

Retrieves and parses the content of a notepad document by its ID. The content is automatically parsed from JSON, so you can access properties directly without additional parsing.

This is useful when you have a notepad ID stored elsewhere (e.g., in a facet, passed as input data, or logged for reference) and need to retrieve its contents.

## Examples

### Basic Retrieval

```jyro
# Retrieve a notepad by ID
var notepadId = "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
var content = GetNotepadContent(notepadId)

if content != null then
    Log("Information", "Notepad type: " + content.reportType)
    Log("Information", "Records: " + content.totalRecords)
else
    Log("Warning", "Notepad not found: " + notepadId)
end
```

### Load Configuration from Previous Execution

```jyro
# Configuration was stored in a previous execution
# The notepad ID is stored in a facet
var configFacet = GetOrganizationFacetInstance(orgId, "LastConfigNotepad")

if configFacet != null and configFacet.values.length > 0 then
    var configNotepadId = configFacet.values[0]
    var config = GetNotepadContent(configNotepadId)

    if config != null then
        Log("Information", "Loaded config: " + config.configName)
        # Use the configuration...
        Data.processingOptions = config.options
    else
        Log("Warning", "Config notepad expired or deleted")
    end
end
```

### Chain Notepad Operations

```jyro
# First, create a notepad with initial data
var initialData = {
    items: [],
    processedAt: Now()
}
var notepadId = CreateNotepad(initialData)

# Later in the script, retrieve and update (create new notepad with more data)
var currentData = GetNotepadContent(notepadId)
if currentData != null then
    # Add more items
    foreach item in newItems do
        currentData.items.add(item)
    end

    # Create a new notepad with updated data
    var updatedNotepadId = CreateNotepad(currentData)
    Log("Information", "Updated data saved to notepad: " + updatedNotepadId)
end
```

### Error Handling Pattern

```jyro
# Robust notepad retrieval with validation
var loadNotepadSafely = fun(id) {
    if id == null or id == "" then
        return null
    end

    var content = GetNotepadContent(id)

    if content == null then
        Log("Warning", "Notepad not found: " + id)
        return null
    end

    # Validate expected structure
    if content.type == null then
        Log("Warning", "Notepad missing required 'type' field")
        return null
    end

    return content
}

var notepadId = Data.inputNotepadId
var data = loadNotepadSafely(notepadId)

if data != null then
    Log("Information", "Successfully loaded notepad of type: " + data.type)
else
    Log("Error", "Failed to load or validate notepad")
end
```

### Cross-Execution Data Sharing

```jyro
# Script A creates a notepad and stores the ID in a facet
var exportData = {
    exportType: "UserList",
    users: GetAllPersons(),
    generatedAt: Now()
}
var notepadId = CreateNotepad(exportData)

# Store the notepad ID for Script B to use later
SetOrganizationFacetInstance(orgId, "LatestExportNotepad", [notepadId])
Log("Information", "Export notepad ID stored: " + notepadId)

# -------- In Script B (runs later) --------

# Retrieve the notepad ID from the facet
var facetData = GetOrganizationFacetInstance(orgId, "LatestExportNotepad")
if facetData != null and facetData.values.length > 0 then
    var storedNotepadId = facetData.values[0]
    var exportedData = GetNotepadContent(storedNotepadId)

    if exportedData != null then
        Log("Information", "Loaded export from " + exportedData.generatedAt)
        Log("Information", "Export contains " + exportedData.users.length + " users")
    end
end
```

## Notes

- Returns null if the notepad is not found, has expired, or belongs to a different tenant
- The content is automatically parsed from JSON - access properties directly
- If the content cannot be parsed as JSON, it's returned as a raw string
- Use `GetNotepads()` if you need to query multiple notepads or don't know the ID
- Notepad content is immutable - to update data, create a new notepad
