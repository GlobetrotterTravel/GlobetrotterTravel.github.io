---
layout: default
title: Log
parent: Iris API functions
has_children: false
has_toc: false
---

# Log

Writes a message to the application logs with the specified severity level.

## Syntax

```jyro
Log(level, message)
```

## Parameters

- **level** (string): Log level - "Debug", "Information", "Warning", or "Error"
- **message** (string): The message to log

## Returns

- **void**: This function does not return a value

## Description

Writes a structured log entry to the application's logging system (Serilog). Log messages are written to configured sinks (console, file, Application Insights, etc.) and include context about the script execution.

Use appropriate log levels:
- **Debug**: Detailed diagnostic information
- **Information**: Informational messages about normal operation
- **Warning**: Potentially problematic situations
- **Error**: Error events that might still allow the application to continue

## Examples

```jyro
# Log informational message
Log("Information", "Processing organization: " + Data.Organization.Name)
```

```jyro
# Log warning for missing data
if Data.Organization.Description == null then
    Log("Warning", "Organization has no description set")
end
```

```jyro
# Log error condition
var org = GetOrganizationById(targetId)
if org == null then
    Log("Error", "Failed to find organization with ID: " + targetId)
    CancelAction("Organization not found")
end
```

```jyro
# Debug logging for troubleshooting
Log("Debug", "Input data: " + Data.Request.body)
Log("Debug", "Processing started")

# ... processing logic ...

Log("Debug", "Processing completed")
```

```jyro
# Log during iteration
var children = GetChildOrganizations(parentId)
Log("Information", "Found " + Length(children) + " child organizations")

foreach child in children do
    Log("Debug", "Processing child: " + child.name + " (ID: " + child.id + ")")
    # ... processing ...
end

Log("Information", "Finished processing all children")
```
