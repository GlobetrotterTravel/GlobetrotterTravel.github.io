---
layout: default
title: Log lines
parent: Automation
grand_parent: Globetrotter Iris
nav_order: 70
permalink: /iris/automation/log-lines/
---

# Log Lines

The `Log()` function allows your scripts to write structured log entries for debugging, auditing, and monitoring. Log lines are captured in the execution history and can be viewed in the Scripting Dashboard.

## The Log Function

```jyro
Log(level, message)
```

**Parameters**:
- `level` (string): The severity level - `"Debug"`, `"Information"`, `"Warning"`, or `"Error"`
- `message` (string): The message to log

**Returns**: Nothing (void)

## Log Levels

Choose the appropriate log level based on the nature of the message:

| Level | Purpose | Examples |
|-------|---------|----------|
| **Debug** | Detailed diagnostic information for troubleshooting | Variable values, loop iterations, function calls |
| **Information** | Normal operational events | Operations completed, records processed, milestones reached |
| **Warning** | Potentially problematic situations that don't prevent operation | Missing optional data, unusual conditions, non-critical issues |
| **Error** | Error conditions that may require attention | Validation failures, API errors, missing required data |

## Basic Usage

```jyro
# Informational messages
Log("Information", "Processing started")
Log("Information", "Found 15 organizations to process")
Log("Information", "Processing completed successfully")

# Debug messages for troubleshooting
Log("Debug", "Input data: " + Data.organization.name)
Log("Debug", "Loop iteration: " + index)

# Warnings for unusual conditions
Log("Warning", "Organization has no description set")
Log("Warning", "Email address format is unusual: " + email)

# Errors for failures
Log("Error", "Failed to find organization with ID: " + orgId)
Log("Error", "API call returned error: " + errorMessage)
```

## Logging Patterns

### Log at Entry and Exit

```jyro
AfterOrganizationCreated:
  Log("Information", "Hook started: AfterOrganizationCreated")

  # Your logic here
  var childCount = Length(GetChildOrganizations(Data.organization.id))
  Log("Debug", "Organization has " + childCount + " children")

  # More processing
  SendNotification(...)

  Log("Information", "Hook completed: AfterOrganizationCreated")
```

### Log Before Important Operations

```jyro
BeforePersonCreated:
  Log("Information", "Validating person: " + Data.person.emailAddress)

  if Data.person.emailAddress == null then
    Log("Error", "Email address is required - cancelling operation")
    CancelAction("Email address is required")
  end

  Log("Information", "Validation passed for: " + Data.person.emailAddress)
```

### Log Loop Progress

```jyro
var organizations = GetAllOrganizations()
Log("Information", "Processing " + Length(organizations) + " organizations")

var processedCount = 0
foreach org in organizations do
  Log("Debug", "Processing: " + org.name + " (" + org.id + ")")

  # Process organization
  processedCount = processedCount + 1
end

Log("Information", "Processed " + processedCount + " organizations")
```

### Log Conditional Branches

```jyro
AfterOrganizationUpdated:
  if Data.previous != null then
    if Data.current.isActive != Data.previous.isActive then
      if Data.current.isActive == true then
        Log("Information", "Organization " + Data.current.name + " was ACTIVATED")
      else
        Log("Warning", "Organization " + Data.current.name + " was DEACTIVATED")
      end
    else
      Log("Debug", "No status change for " + Data.current.name)
    end
  end
```

### Log with Context

Include relevant identifiers and context in your messages:

```jyro
# Include entity IDs for traceability
Log("Information", "[Org:" + Data.organization.id + "] Processing: " + Data.organization.name)

# Include counts and metrics
Log("Information", "Batch complete: " + successCount + " succeeded, " + failCount + " failed")

# Include user context
var user = GetCurrentUser()
Log("Information", "Operation performed by: " + user.name)
```

## Viewing Log Lines

### Execution History

1. Navigate to `Administration` > `Scripting` > `Execution History`
2. Find the execution you want to inspect
3. Click to expand the execution details
4. View the `Logs` section

Each log entry shows:
- Timestamp
- Log level
- Message

### Filtering by Level

In the execution details, logs are displayed with color-coding:

- **Debug**: Gray - detailed diagnostic info
- **Information**: Blue - normal operations
- **Warning**: Yellow - potential issues
- **Error**: Red - failures or problems

## Best Practices

### 1. Use Appropriate Levels

```jyro
# Good - appropriate levels
Log("Debug", "Variable value: " + someVar)           # Detailed diagnostics
Log("Information", "Processing started")              # Normal operations
Log("Warning", "Optional field missing")              # Potential issues
Log("Error", "Required field missing")                # Actual problems

# Avoid - wrong levels
Log("Error", "Processing started")                    # Not an error
Log("Debug", "Critical failure occurred")             # Should be Error
```

### 2. Include Actionable Context

```jyro
# Good - actionable context
Log("Error", "Organization not found. ID: " + orgId + ". Check if it was deleted.")

# Avoid - vague messages
Log("Error", "Something went wrong")
```

### 3. Log Decision Points

```jyro
# Log why a decision was made
if org.isActive == false then
  Log("Information", "Skipping inactive organization: " + org.name)
  continue
end
```

### 4. Don't Over-Log

```jyro
# Avoid logging every minor step
foreach item in items do
  # Log("Debug", "About to check item")        # Too verbose
  # Log("Debug", "Checked item")               # Too verbose

  if item.isImportant then
    Log("Debug", "Processing important item: " + item.id)  # Meaningful
  end
end
```

### 5. Avoid Sensitive Data

```jyro
# Avoid - logs sensitive information
Log("Debug", "API key: " + apiKey)
Log("Debug", "Password: " + password)

# Better - log only what's needed
Log("Debug", "API key configured: " + (apiKey != null))
Log("Debug", "Authenticating user: " + username)
```

## Troubleshooting with Logs

### Script Not Running

Add entry logging to confirm the script is being triggered:

```jyro
AfterOrganizationCreated:
  Log("Information", "=== AfterOrganizationCreated triggered ===")
  Log("Debug", "Organization ID: " + Data.organization.id)
  Log("Debug", "Organization Name: " + Data.organization.name)

  # Rest of script
```

### Finding Where Errors Occur

Add logging before each major section:

```jyro
Log("Debug", "Step 1: Getting organization")
var org = GetOrganizationById(orgId)

Log("Debug", "Step 2: Getting children")
var children = GetChildOrganizations(org.id)

Log("Debug", "Step 3: Processing children")
foreach child in children do
  Log("Debug", "Step 3a: Processing " + child.name)
  # If script fails here, you'll see the last log was "Step 3a"
end

Log("Debug", "Step 4: Completed")
```

### Debugging Unexpected Values

Log variable values to understand what's happening:

```jyro
Log("Debug", "Facet instances count: " + Length(Data.organization.facetInstances))

foreach facet in Data.organization.facetInstances do
  Log("Debug", "Facet ID: " + facet.facetDefinitionId)
  Log("Debug", "Values count: " + Length(facet.values))

  foreach value in facet.values do
    Log("Debug", "  Value: '" + value + "'")
  end
end
```

## Next Steps

- [Notepads](notepads) - Store structured data for later retrieval
- [Event Queue](event-queue) - View execution history and logs
- [Event Hooks](event-hooks) - Use logging in event hooks
