---
layout: default
title: GetNotepads
parent: Iris API functions
has_children: false
has_toc: false
---

# GetNotepads

Retrieves Notepad documents created during script execution. Can query notepads from the current execution or a specific execution ID.

## Syntax

```jyro
GetNotepads()
GetNotepads(options)
```

## Parameters

- **options** (object, optional): Query options
  - **executionId** (string, optional): Specific execution ID to query (defaults to current execution)
  - **sourceId** (string, optional): Filter by source script ID

## Returns

- **array**: Array of notepad objects, each containing:
  - **id** (string): The notepad ID (GUID)
  - **sourceId** (string): The source script ID (if applicable)
  - **executionId** (string): The execution ID that created the notepad
  - **createdAt** (string): ISO 8601 timestamp of creation
  - **createdBy** (string): The user/system that created the notepad
  - **content** (string): The notepad content (JSON string)

## Description

Retrieves notepad documents that were created during script execution. By default, returns notepads from the current script execution, but can be configured to query notepads from other executions.

This is useful for:
- **Accessing intermediate data**: Retrieve notepads created earlier in the same execution
- **Cross-execution queries**: Load data from previous script runs
- **Debugging**: Inspect what was captured during a specific execution

## Examples

### Get All Notepads from Current Execution

```jyro
# Get all notepads created so far in this execution
var notepads = GetNotepads()

Log("Information", "Found " + notepads.length + " notepads")

foreach notepad in notepads do
    Log("Debug", "Notepad " + notepad.id + " created at " + notepad.createdAt)
end
```

### Query Specific Execution

```jyro
# Query notepads from a previous execution (e.g., stored in a facet)
var previousExecutionId = GetOrganizationFacetInstance(orgId, facetDefId)

if previousExecutionId != null then
    var previousNotepads = GetNotepads({
        executionId: previousExecutionId.values[0]
    })

    Log("Information", "Previous execution had " + previousNotepads.length + " notepads")

    # Process data from previous run
    foreach notepad in previousNotepads do
        var content = notepad.content
        # content is already a parsed object
        Log("Information", "Processing data from notepad: " + notepad.id)
    end
end
```

### Filter by Source Script

```jyro
# Get notepads created by a specific script in the current execution
var myScriptId = "638bd6bb-2024-471f-8724-5e883a03a93d"

var scriptNotepads = GetNotepads({
    sourceId: myScriptId
})

if scriptNotepads.length > 0 then
    Log("Information", "Found " + scriptNotepads.length + " notepads from this script")
end
```

### Aggregate Data from Multiple Notepads

```jyro
# Create notepads during processing
foreach batch in batches do
    var batchResult = ProcessBatch(batch)
    CreateNotepad({
        batchId: batch.id,
        processedCount: batchResult.count,
        errors: batchResult.errors
    })
end

# After all batches, aggregate the results
var allNotepads = GetNotepads()
var totalProcessed = 0
var allErrors = []

foreach notepad in allNotepads do
    var data = notepad.content
    totalProcessed = totalProcessed + data.processedCount
    if data.errors.length > 0 then
        foreach error in data.errors do
            allErrors.add(error)
        end
    end
end

# Create summary notepad
CreateNotepad({
    type: "BatchSummary",
    totalBatches: batches.length,
    totalProcessed: totalProcessed,
    totalErrors: allErrors.length,
    errors: allErrors
})
```

## Notes

- The **content** property is already parsed from JSON - no need to parse it again
- Returns an empty array if no notepads are found (not null)
- Querying other executions requires knowing the execution ID
- The function executes synchronously for immediate access to results
- Notepads are tenant-scoped - you can only query notepads in your tenant
