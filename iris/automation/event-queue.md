---
layout: default
title: Event queue
parent: Automation
grand_parent: Globetrotter Iris
nav_order: 90
permalink: /iris/automation/event-queue/
---

# Event Queue & Reporting

The event queue handles asynchronous script execution, while execution history provides visibility into all script runs - successful and failed.

## How the Event Queue Works

When async event hooks or scheduled scripts run, they're processed through a background job queue:

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Trigger        │     │  Job Queue      │     │  Execution      │
│                 │     │                 │     │                 │
│  - Async Hook   │────>│  - Queued       │────>│  - Running      │
│  - Schedule     │     │  - Processing   │     │  - Completed    │
│  - Manual Run   │     │                 │     │  - Failed       │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

1. **Trigger**: An async event hook fires, a schedule triggers, or you manually run a script
2. **Queue**: The job is added to the background queue
3. **Execution**: A worker picks up the job and runs the script
4. **Recording**: The result is saved to execution history

## Execution History

All script executions are recorded in the execution history, regardless of how they were triggered:

- Event hooks (Before, After, Async)
- Scheduled scripts
- Dynamic endpoints
- Ad-hoc script runs

### Viewing Execution History

1. Navigate to `Administration` > `Scripting` > `Execution History`
2. View the list of executions

Each execution shows:

| Field | Description |
|-------|-------------|
| **Script Name** | Name of the script that ran |
| **Execution Type** | EventHook, ScheduledScript, DynamicEndpoint, AdHoc |
| **Status** | Success, Failed, or Cancelled |
| **Started At** | When the execution began |
| **Duration** | How long the execution took |
| **Trigger** | What caused the execution |

### Execution Details

Click on an execution to see detailed information:

**Timing Information**:
- Started At
- Completed At
- Execution Time (milliseconds)
- Statement Count (Jyro statements executed)
- Loop Count (loop iterations executed)

**Input Data**:
- The data that was passed to the script (organization, person, request, etc.)
- Input data snapshot for debugging

**Output**:
- Output summary (first 1KB)
- Full output data (for dynamic endpoints)
- Status code (for dynamic endpoints)

**Logs**:
- All `Log()` entries generated during execution
- Timestamped with level (Debug, Information, Warning, Error)

**Notepads**:
- List of notepads created during execution
- Click to view notepad content

**Errors** (if failed):
- Error message
- Cancellation reason (if cancelled via `CancelAction()`)

## Filtering Executions

### By Status

- **All**: Show all executions
- **Succeeded**: Only successful executions
- **Failed**: Only failed executions
- **Cancelled**: Only cancelled executions (via `CancelAction()`)

### By Execution Type

- **EventHook**: Event hook executions
- **ScheduledScript**: Scheduled script runs
- **DynamicEndpoint**: Dynamic endpoint invocations
- **AdHoc**: Manual script runs

### By Date Range

Filter to specific time periods:
- Last hour
- Last 24 hours
- Last 7 days
- Custom date range

### By Script

Search for executions of a specific script by name.

## Monitoring Script Performance

### Execution Statistics

View aggregate statistics for your scripts:

| Metric | Description |
|--------|-------------|
| **Total Executions** | Total number of runs |
| **Success Rate** | Percentage of successful runs |
| **Average Duration** | Mean execution time |
| **Statement Count** | Average statements executed |

### Identifying Issues

Look for these warning signs:

**High Failure Rate**:
- Many failed executions
- Check error messages for patterns
- Review input data that caused failures

**Long Execution Times**:
- Scripts taking longer than expected
- May need optimization
- Consider using async hooks for heavy processing

**Cancelled Executions**:
- Scripts calling `CancelAction()`
- May indicate validation issues
- Review the cancellation reasons

## Troubleshooting Failed Executions

### 1. Check the Error Message

The execution details show what went wrong:

```
Error: Organization not found with ID: 12345
```

### 2. Review the Logs

Log entries show where the script was when it failed:

```
[Information] Processing started
[Debug] Found 5 organizations
[Debug] Processing org: Acme Corp
[Error] Failed to get child organizations
```

### 3. Examine Input Data

The input data snapshot shows what was passed to the script:

```json
{
  "organization": {
    "id": "abc-123",
    "name": "Test Org",
    "isActive": true
  }
}
```

### 4. Check Notepads

If the script created diagnostic notepads before failing, they'll contain valuable debugging information.

### 5. Reproduce with Ad-Hoc Runner

Use the Script Runner to test with similar input:

1. Go to `Administration` > `Scripting` > `Script Runner`
2. Paste the script content
3. Set up similar input data
4. Run and observe the behavior

## Execution Retention

Execution history is retained for 365 days by default:

- After 365 days, executions are automatically deleted
- Associated notepads are deleted with the execution
- Statistics are preserved even after detail deletion

> 🛈 If you need to keep execution data longer, export it or create notepads in facets that persist independently.

## Event Audit Trail

For domain events (creates, updates, deletes), additional audit information is captured:

| Field | Description |
|-------|-------------|
| **Event Type** | What happened (Created, Updated, Deleted) |
| **Entity Type** | What type of entity (Organization, Person, etc.) |
| **Entity ID** | The ID of the affected entity |
| **Scripts Executed** | How many hooks ran |
| **Successful Scripts** | How many succeeded |
| **Failed Scripts** | How many failed |

### Per-Script Details

For each hook that ran:

- Script name
- Priority
- Status (Success, Failed, Cancelled)
- Execution time
- Error message (if failed)

## Async Processing

### How Async Hooks Work

Async hooks are queued and processed in the background:

1. **Domain event occurs** (e.g., organization created)
2. **Async hooks are identified** for that event
3. **Jobs are queued** for each async hook
4. **Background worker picks up jobs** in order
5. **Scripts execute independently** of the original request
6. **Results are recorded** in execution history

### Benefits of Async Processing

- **Non-blocking**: Original request completes immediately
- **Reliability**: Failed jobs can be retried
- **Scalability**: Heavy processing doesn't slow down the UI
- **Visibility**: All executions are tracked and viewable

### Limitations

- **No cancellation**: Async hooks cannot call `CancelAction()`
- **No modification**: Data is already persisted, so modifications don't affect the saved record
- **Eventual consistency**: Processing happens after the fact

## Best Practices

### 1. Monitor Regularly

Check execution history periodically for:
- Increasing failure rates
- Unusually long execution times
- Unexpected patterns

### 2. Set Up Alerts

Use log levels appropriately so monitoring tools can alert on issues:
```jyro
Log("Error", "Critical failure in daily processing")  # Triggers alert
```

### 3. Use Notepads for Debugging

Create diagnostic notepads to capture state:
```jyro
CreateNotepad({
  checkpoint: "before-api-call",
  data: inputData
})
```

### 4. Keep Scripts Fast

For Before and After hooks:
- Minimize database calls
- Avoid external API calls
- Use async hooks for heavy processing

### 5. Handle Failures Gracefully

Design scripts to fail gracefully:
```jyro
var result = GetOrganizationById(orgId)

if result == null then
  Log("Warning", "Organization not found, skipping")
  return
end

# Continue processing
```

## Next Steps

- [Event Hooks](event-hooks) - Understand the different hook timing types
- [Log Lines](log-lines) - Use logging for better troubleshooting
- [Notepads](notepads) - Store diagnostic data for debugging
