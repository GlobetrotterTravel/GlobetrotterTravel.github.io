---
layout: default
title: DeleteNotepad
parent: Iris API functions
has_children: false
has_toc: false
---

# DeleteNotepad

Deletes a Notepad document by its ID.

## Syntax

```jyro
DeleteNotepad(notepadId)
```

## Parameters

- **notepadId** (string): The notepad ID (GUID) to delete

## Returns

- **boolean**: true if the notepad was deleted, false if not found or error occurred

## Description

Permanently deletes a notepad document from the database. This is useful for:
- **Cleanup**: Remove temporary notepads after processing is complete
- **Privacy**: Delete notepads containing sensitive data
- **Storage Management**: Remove large notepads that are no longer needed

## Examples

### Basic Deletion

```jyro
var notepadId = "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
var deleted = DeleteNotepad(notepadId)

if deleted then
    Log("Information", "Notepad deleted successfully")
else
    Log("Warning", "Notepad not found or already deleted")
end
```

### Clean Up After Processing

```jyro
# Create temporary notepad for intermediate data
var tempNotepadId = CreateNotepad({
    type: "temp",
    processingData: Data.inputItems
})

# Process the data...
var results = ProcessItems(Data.inputItems)

# Create final results notepad
var finalNotepadId = CreateNotepad({
    type: "results",
    processedAt: Now(),
    results: results
})

# Delete the temporary notepad
var cleaned = DeleteNotepad(tempNotepadId)
if cleaned then
    Log("Debug", "Cleaned up temporary notepad")
end

Log("Information", "Final results saved to: " + finalNotepadId)
```

### Delete Sensitive Data

```jyro
# Process sensitive data
var apiCredentials = GetSecret("ExternalApiKey")
var sensitiveResponse = InvokeRestMethod(
    "POST",
    "https://api.example.com/sensitive",
    { "Authorization": "Bearer " + apiCredentials },
    Data.payload
)

# Temporarily store for debugging
var debugNotepadId = CreateNotepad({
    response: sensitiveResponse,
    timestamp: Now()
})

# Process the response
if sensitiveResponse.status == "success" then
    # Success - delete the sensitive data notepad
    DeleteNotepad(debugNotepadId)
    Log("Information", "Processed successfully, debug data removed")
else
    # Keep for debugging, but log warning
    Log("Warning", "Processing failed. Debug notepad retained: " + debugNotepadId)
end
```

### Batch Cleanup

```jyro
# Get all notepads from a previous execution that should be cleaned up
var oldExecutionId = GetOrganizationFacetInstance(orgId, "PreviousExecutionId")

if oldExecutionId != null and oldExecutionId.values.length > 0 then
    var oldNotepads = GetNotepads({
        executionId: oldExecutionId.values[0]
    })

    var deletedCount = 0
    foreach notepad in oldNotepads do
        # Only delete temporary notepads
        var content = notepad.content
        if content.type == "temp" then
            var deleted = DeleteNotepad(notepad.id)
            if deleted then
                deletedCount = deletedCount + 1
            end
        end
    end

    Log("Information", "Cleaned up " + deletedCount + " temporary notepads from previous execution")
end
```

### Conditional Deletion with Error Handling

```jyro
# Delete a notepad only if it exists and meets criteria
var notepadId = Data.notepadToDelete

if notepadId == null or notepadId == "" then
    Log("Warning", "No notepad ID provided for deletion")
else
    # First, check if it exists and is safe to delete
    var content = GetNotepadContent(notepadId)

    if content == null then
        Log("Information", "Notepad already deleted or doesn't exist")
    else if content.protected == true then
        Log("Warning", "Cannot delete protected notepad: " + notepadId)
    else
        var deleted = DeleteNotepad(notepadId)
        if deleted then
            Log("Information", "Successfully deleted notepad: " + notepadId)
        else
            Log("Error", "Failed to delete notepad: " + notepadId)
        end
    end
end
```

## Notes

- Deletion is permanent - there is no undo or recycle bin
- Returns false if the notepad doesn't exist (idempotent operation)
- You can only delete notepads in your own tenant
- The function executes synchronously and waits for confirmation
- Consider the notepad's importance before deletion - it may be needed for auditing
