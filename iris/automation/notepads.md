---
layout: default
title: Notepads
parent: Automation
grand_parent: Globetrotter Iris
nav_order: 80
permalink: /iris/automation/notepads/
---

# Notepads

Notepads are persistent JSON documents that scripts create to store data for later retrieval. They're automatically linked to the script execution and can be viewed in the Scripting Dashboard.

## What Are Notepads?

Unlike log lines that capture text messages, notepads store structured data. This makes them ideal for:

- **Debugging**: Capture the state of variables and objects at specific points
- **Auditing**: Record decisions, inputs, and outputs for compliance
- **Reports**: Generate data that can be retrieved later
- **Cross-script communication**: Store data that another script can access

Each notepad is automatically associated with:
- The current execution ID
- The source script (event hook, scheduled script, etc.)
- The tenant
- A creation timestamp

## Creating Notepads

Use `CreateNotepad()` to create a new notepad:

```jyro
var notepadId = CreateNotepad({
  reportType: "Daily Summary",
  generatedAt: Now(),
  totalOrganizations: 42,
  activeCount: 38,
  inactiveCount: 4
})

Log("Information", "Created notepad: " + notepadId)
```

The function accepts any Jyro object (nested objects, arrays, strings, numbers, booleans) and returns the notepad ID.

### Examples

**Simple data**:
```jyro
CreateNotepad({
  message: "Script completed successfully",
  processedCount: 15
})
```

**Complex nested data**:
```jyro
CreateNotepad({
  summary: {
    total: 100,
    succeeded: 95,
    failed: 5
  },
  failures: [
    { id: "org-1", reason: "Invalid data" },
    { id: "org-2", reason: "Permission denied" }
  ],
  metadata: {
    executedBy: GetCurrentUser().name,
    executedAt: Now()
  }
})
```

**Capturing entity state**:
```jyro
AfterOrganizationCreated:
  CreateNotepad({
    event: "OrganizationCreated",
    organization: {
      id: Data.organization.id,
      name: Data.organization.name,
      description: Data.organization.description,
      facetCount: Length(Data.organization.facetInstances)
    },
    timestamp: Now()
  })
```

## Retrieving Notepads

### Get All Notepads from Current Execution

```jyro
var notepads = GetNotepads()

foreach notepad in notepads do
  Log("Information", "Notepad ID: " + notepad.id)
  Log("Information", "Created: " + notepad.createdAt)

  # Access the content directly (already parsed)
  var content = notepad.content
  Log("Information", "Report type: " + content.reportType)
end
```

### Get Notepads from a Specific Execution

```jyro
var previousExecutionId = "guid-of-previous-execution"

var notepads = GetNotepads({
  executionId: previousExecutionId
})
```

### Get Notepads from a Specific Source Script

```jyro
var eventHookId = "guid-of-event-hook"

var notepads = GetNotepads({
  sourceId: eventHookId
})
```

### Get a Single Notepad by ID

```jyro
var notepadId = "guid-of-notepad"
var content = GetNotepadContent(notepadId)

if content != null then
  Log("Information", "Found: " + content.reportType)
else
  Log("Warning", "Notepad not found")
end
```

## Viewing Notepads in the Dashboard

1. Navigate to `Administration` > `Scripting` > `Execution History`
2. Find the execution that created the notepads
3. Click to expand the execution details
4. View the `Notepads` section
5. Click on a notepad to see its content

The dashboard displays:
- Notepad ID
- Creation timestamp
- Content preview
- Full JSON content (expandable)

## Common Patterns

### Error Capture at Failure Points

Capture data before risky operations to aid debugging:

```jyro
var externalData = InvokeRestMethod("GET", "https://api.example.com/data", {}, {})

# Capture the response for debugging
var debugId = CreateNotepad({
  timestamp: Now(),
  apiUrl: "https://api.example.com/data",
  response: externalData,
  context: {
    organizationId: Data.organization.id,
    organizationName: Data.organization.name
  }
})

if externalData.status != "success" then
  Log("Error", "API failed. Debug notepad: " + debugId)
  CancelAction("External API returned error")
end
```

### Batch Processing Results

Track results across multiple operations:

```jyro
var results = {
  processed: 0,
  succeeded: 0,
  failed: 0,
  failures: []
}

foreach org in organizations do
  results.processed = results.processed + 1

  var success = ProcessOrganization(org)

  if success then
    results.succeeded = results.succeeded + 1
  else
    results.failed = results.failed + 1
    results.failures.add({
      id: org.id,
      name: org.name,
      error: "Processing failed"
    })
  end
end

# Save the results
CreateNotepad({
  type: "BatchProcessingResults",
  completedAt: Now(),
  results: results
})
```

### Cross-Script Data Sharing

Store data in one script, retrieve it in another:

**Script A - Store Configuration**:
```jyro
var config = {
  processingMode: "batch",
  batchSize: 100,
  maxRetries: 3,
  createdAt: Now()
}

var configNotepadId = CreateNotepad(config)

# Store the notepad ID in a facet for Script B to find
var configFacetId = GetFacetDefinitionByName("ConfigNotepad")
SetOrganizationFacetInstance(orgId, configFacetId, [configNotepadId])

Log("Information", "Config stored in notepad: " + configNotepadId)
```

**Script B - Retrieve Configuration**:
```jyro
# Get the notepad ID from the facet
var configFacetId = GetFacetDefinitionByName("ConfigNotepad")
var facetData = GetOrganizationFacetInstance(orgId, configFacetId)

if facetData != null and Length(facetData.values) > 0 then
  var configNotepadId = facetData.values[0]
  var config = GetNotepadContent(configNotepadId)

  if config != null then
    Log("Information", "Using batch size: " + config.batchSize)
    # Use the config...
  end
end
```

### Audit Trail

Create notepads for compliance and auditing:

```jyro
AfterOrganizationDeleted:
  CreateNotepad({
    auditEvent: "OrganizationDeleted",
    organization: {
      id: Data.organization.id,
      name: Data.organization.name,
      description: Data.organization.description,
      wasActive: Data.organization.isActive
    },
    deletedBy: GetCurrentUser().name,
    deletedAt: Now(),
    facetSnapshot: Data.organization.facetInstances,
    fieldSnapshot: Data.organization.fieldInstances
  })

  Log("Information", "Audit notepad created for deleted organization: " + Data.organization.name)
```

### Report Generation

Create structured reports:

```jyro
# Daily organization report
var orgs = GetAllOrganizations()
var activeOrgs = []
var inactiveOrgs = []

foreach org in orgs do
  if org.isActive == true then
    activeOrgs.add({
      id: org.id,
      name: org.name,
      childCount: Length(GetChildOrganizations(org.id))
    })
  else
    inactiveOrgs.add({
      id: org.id,
      name: org.name
    })
  end
end

CreateNotepad({
  reportType: "DailyOrganizationSummary",
  generatedAt: Now(),
  generatedBy: GetCurrentUser().name,
  totals: {
    total: Length(orgs),
    active: Length(activeOrgs),
    inactive: Length(inactiveOrgs)
  },
  activeOrganizations: activeOrgs,
  inactiveOrganizations: inactiveOrgs
})
```

## Best Practices

### 1. Include Metadata

Always include timestamps and context:

```jyro
CreateNotepad({
  createdAt: Now(),
  createdBy: GetCurrentUser().name,
  scriptName: "MyEventHook",
  # ... your data
})
```

### 2. Use Descriptive Types

Include a type or category for easy filtering:

```jyro
CreateNotepad({
  type: "ValidationError",
  # ...
})

CreateNotepad({
  type: "ProcessingReport",
  # ...
})
```

### 3. Keep Size Reasonable

Notepads are stored in the database. Avoid storing very large datasets:

```jyro
# Good - summary data
CreateNotepad({
  processedCount: 10000,
  failureCount: 5,
  failures: failedItems  # Just the 5 failures, not all 10000
})

# Avoid - full dataset
# CreateNotepad({ allItems: allTenThousandItems })
```

### 4. Capture Before Operations

Create diagnostic notepads before risky operations:

```jyro
# Capture state before making changes
var diagnosticId = CreateNotepad({
  beforeState: org,
  attemptedOperation: "update",
  parameters: updateParams
})

# Now try the operation
var result = UpdateOrganization(org.id, updateParams)

if result == false then
  Log("Error", "Update failed. See notepad: " + diagnosticId)
end
```

## Notepad Retention

Notepads are stored with execution history and follow the same retention policy:

- Default retention: 365 days
- Notepads are automatically deleted when their execution history expires
- To keep data permanently, export it or store in facets

## Next Steps

- [Log Lines](log-lines) - Combine logging with notepads for complete debugging
- [Event Queue](event-queue) - View notepad data in execution history
- [Event Hooks](event-hooks) - Create notepads in event hooks
