---
layout: default
title: Script scheduling
parent: Automation
grand_parent: Globetrotter Iris
nav_order: 40
permalink: /iris/automation/script-scheduling/
---

# Script Scheduling

Scheduled scripts run automatically at defined intervals or times. Use them for recurring tasks like daily reports, periodic cleanup, or regular data synchronization.

## Schedule Types

| Type | Description | Example Use Case |
|------|-------------|------------------|
| **Interval** | Fixed time intervals | Run every 5 minutes |
| **Cron** | Standard cron expressions | Complex schedules (e.g., weekdays at 9am) |
| **Daily** | Specific time each day | Daily report at 6:00 AM |
| **Weekly** | Specific days and time | Monday and Friday at 9:00 AM |
| **Monthly** | Specific days of month | 1st and 15th at midnight |
| **OneTime** | Single execution | One-time migration task |

## Creating a Scheduled Script

1. Navigate to `Administration` > `Scripting` > `Scheduled Scripts`
2. Click `+ Create Scheduled Script`
3. Fill in the configuration:
   - **Name**: Descriptive name for the schedule
   - **Description**: What the script does
   - **Schedule Type**: Choose from the options above
   - **Schedule Configuration**: Depends on type (see below)
   - **Timezone**: The timezone for schedule calculations
   - **Script Content**: Your Jyro script
   - **Input Data** (optional): JSON data passed to the script
   - **Is Active**: Enable/disable the schedule
4. Click `Save`

## Schedule Configuration

### Interval Schedule

Run the script at fixed intervals:

- **Interval**: Time between runs (e.g., 5 minutes, 1 hour)

Example: Run every 15 minutes

```
Interval: 00:15:00
```

> ⚠ Minimum interval is 1 minute. For very frequent tasks, consider whether an event hook might be more appropriate.

### Cron Schedule

Use standard 5-field cron expressions for complex schedules:

```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of week (0 - 6, Sunday = 0)
│ │ │ │ │
* * * * *
```

Common examples:

| Expression | Meaning |
|------------|---------|
| `0 * * * *` | Every hour at minute 0 |
| `0 9 * * *` | Every day at 9:00 AM |
| `0 9 * * 1-5` | Weekdays at 9:00 AM |
| `0 0 1 * *` | 1st of each month at midnight |
| `*/15 * * * *` | Every 15 minutes |
| `0 9,17 * * *` | At 9:00 AM and 5:00 PM |

### Daily Schedule

Run at a specific time each day:

- **Run Time**: Time in HH:mm format (e.g., `06:00` for 6:00 AM)

### Weekly Schedule

Run on specific days at a specific time:

- **Run Time**: Time in HH:mm format
- **Days of Week**: Select which days (Monday, Tuesday, etc.)

Example: Monday, Wednesday, Friday at 8:00 AM

### Monthly Schedule

Run on specific days of the month:

- **Run Time**: Time in HH:mm format
- **Days of Month**: Select which days (1, 15, etc.)

Example: 1st and 15th of each month at midnight

### OneTime Schedule

Run once at a specific date and time:

- **Run Date**: The date to run (yyyy-MM-dd)
- **Run Time**: Time in HH:mm format

> 🛈 OneTime schedules automatically deactivate after running. They won't run again unless you reactivate them with a new date.

## Timezone Handling

Scheduled scripts respect timezones for accurate scheduling:

1. Set the **Timezone** field to your preferred timezone (e.g., `America/New_York`, `Europe/London`)
2. All times are interpreted in that timezone
3. Daylight saving time transitions are handled automatically

Default timezone is UTC if not specified.

## Input Data

Pass configuration data to your script via the **Input Data** field (JSON format):

```json
{
  "batchSize": 100,
  "targetOrganizationId": "abc-123",
  "notifyOnComplete": true
}
```

Access this data in your script:

```jyro
var batchSize = Data.input.batchSize
var targetId = Data.input.targetOrganizationId

Log("Information", "Processing with batch size: " + batchSize)
```

## Execution Tracking

Each scheduled script tracks its execution history:

| Field | Description |
|-------|-------------|
| **Last Run At** | When the script last ran |
| **Next Run At** | When the script will run next |
| **Run Count** | Total number of executions |
| **Success Count** | Number of successful runs |
| **Failure Count** | Number of failed runs |
| **Last Error** | Error message from last failure (if any) |

View this information in the Scheduled Scripts list or detail view.

## Manual Execution

Run a scheduled script immediately without waiting for the schedule:

1. Navigate to `Administration` > `Scripting` > `Scheduled Scripts`
2. Find the script you want to run
3. Click the **Run Now** button

The script runs immediately with the configured input data. The next scheduled run is not affected.

## Common Use Cases

### Daily Report

Generate a daily summary of organization activity:

```jyro
# Daily Organization Report - runs at 6:00 AM

var orgs = GetAllOrganizations()
var activeCount = 0
var inactiveCount = 0
var newToday = 0

var today = Now()

foreach org in orgs do
  if org.isActive == true then
    activeCount = activeCount + 1
  else
    inactiveCount = inactiveCount + 1
  end
end

# Create report notepad
CreateNotepad({
  reportType: "DailyOrganizationSummary",
  date: today,
  totals: {
    active: activeCount,
    inactive: inactiveCount,
    total: Length(orgs)
  }
})

# Notify administrators
BroadcastNotification(
  "Daily Report Ready",
  "Organization summary: " + activeCount + " active, " + inactiveCount + " inactive",
  "Information",
  "/reports/daily",
  true
)

Log("Information", "Daily report completed: " + activeCount + " active organizations")
```

### Periodic Cleanup

Clean up inactive records weekly:

```jyro
# Weekly Cleanup - runs every Monday at 2:00 AM

var orgs = GetAllOrganizations()
var deactivatedCount = 0

foreach org in orgs do
  # Check for organizations with expired entitlements
  var hasActiveEntitlement = false

  foreach entitlement in org.entitlementInstances do
    if entitlement.expiresAt == null or entitlement.expiresAt > Now() then
      hasActiveEntitlement = true
    end
  end

  if hasActiveEntitlement == false and org.isActive == true then
    Log("Warning", "Organization has no active entitlements: " + org.name)
    deactivatedCount = deactivatedCount + 1
  end
end

CreateNotepad({
  reportType: "WeeklyCleanupReport",
  date: Now(),
  organizationsWithExpiredEntitlements: deactivatedCount
})

Log("Information", "Weekly cleanup complete: " + deactivatedCount + " organizations flagged")
```

### Data Synchronization

Sync data from an external system every hour:

```jyro
# Hourly Sync - runs every hour

var apiUrl = Data.input.apiUrl
var apiKey = GetSecret("external-api-key")

var headers = {
  "Authorization": "Bearer " + apiKey
}

var response = InvokeRestMethod("GET", apiUrl + "/organizations", headers, null)

if response.status == 200 then
  var externalOrgs = response.body.data
  var syncedCount = 0

  foreach extOrg in externalOrgs do
    # Check if org exists
    var existing = GetOrganizationById(extOrg.irisId)

    if existing != null then
      # Update facets with external data
      var externalIdFacet = GetFacetDefinitionByName("External ID")
      SetOrganizationFacetInstance(existing.id, externalIdFacet, [extOrg.externalId])
      syncedCount = syncedCount + 1
    end
  end

  Log("Information", "Sync complete: " + syncedCount + " organizations updated")
else
  Log("Error", "External API returned status: " + response.status)
end
```

### One-Time Migration

Run a migration task once:

```jyro
# One-Time Migration - runs once then deactivates

var orgs = GetAllOrganizations()
var migratedCount = 0

# Get the new facet definition
var newFacetId = GetFacetDefinitionByName("New Category")
var oldFacetId = GetFacetDefinitionByName("Legacy Category")

foreach org in orgs do
  # Check if org has old facet
  var oldFacet = GetOrganizationFacetInstance(org.Id, oldFacetId)

  if oldFacet != null and Length(oldFacet.Values) > 0 then
    # Migrate to new facet
    SetOrganizationFacetInstance(org.Id, newFacetId, oldFacet.Values)

    # Remove old facet
    RemoveOrganizationFacetInstance(org.Id, oldFacetId)

    migratedCount = migratedCount + 1
    Log("Information", "Migrated: " + org.Name)
  end
end

CreateNotepad({
  migrationType: "FacetMigration",
  migratedAt: Now(),
  organizationsMigrated: migratedCount
})

Log("Information", "Migration complete: " + migratedCount + " organizations migrated")
```

## Activating and Deactivating

### Activate a Schedule

1. Navigate to the scheduled script
2. Click **Activate**
3. The schedule becomes active and will run at the next scheduled time

### Deactivate a Schedule

1. Navigate to the scheduled script
2. Click **Deactivate**
3. The schedule stops running but configuration is preserved

Use deactivation for:
- Temporary maintenance
- Debugging issues
- Seasonal schedules

## Best Practices

### 1. Use Descriptive Names

```
Good: "Daily Organization Summary Report"
Bad: "Script 1"
```

### 2. Log Progress

```jyro
Log("Information", "Scheduled job started: Daily Report")
# ... processing ...
Log("Information", "Scheduled job completed: 150 records processed")
```

### 3. Create Result Notepads

Store results for auditing and debugging:

```jyro
CreateNotepad({
  jobName: "Daily Report",
  completedAt: Now(),
  recordsProcessed: count,
  errors: errorList
})
```

### 4. Handle Errors Gracefully

```jyro
foreach org in orgs do
  var result = ProcessOrganization(org)

  if result == false then
    Log("Warning", "Failed to process: " + org.Name)
    # Continue with next org instead of failing entire job
  end
end
```

### 5. Consider Timing

- Run heavy processing during off-peak hours
- Avoid scheduling multiple heavy scripts at the same time
- Use appropriate intervals (not too frequent)

### 6. Monitor Execution

Regularly check:
- Execution history for failures
- Duration trends for performance issues
- Success/failure rates

## Next Steps

- [Event Queue](event-queue) - Monitor scheduled script executions
- [Log Lines](log-lines) - Add logging to scheduled scripts
- [Notepads](notepads) - Store scheduled job results
