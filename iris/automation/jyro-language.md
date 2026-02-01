---
layout: default
title: Jyro language
parent: Automation
grand_parent: Globetrotter Iris
nav_order: 10
permalink: /iris/automation/jyro-language/
---

# The Jyro Scripting Language

Jyro is the scripting language used for all automation in Globetrotter.Iris3. It provides a simple, readable syntax for writing event hooks, scheduled scripts, and dynamic endpoints.

## Basic Syntax

### Variables

```jyro
var name = "Acme Corp"
var count = 42
var isActive = true
var items = ["apple", "banana", "cherry"]
var config = { timeout: 30, retries: 3 }
```

### Conditionals

```jyro
if org.isActive == true then
  Log("Information", "Organization is active")
else
  Log("Warning", "Organization is inactive")
end

# Or with multiple conditions
if count > 100 then
  Log("Information", "High volume")
elif count > 50 then
  Log("Information", "Medium volume")
else
  Log("Information", "Low volume")
end
```

### Loops

```jyro
# Iterate over a collection
foreach org in organizations do
  Log("Information", "Processing: " + org.name)
end

# With index
foreach item, index in items do
  Log("Information", "Item " + index + ": " + item)
end
```

### Functions

Jyro provides host functions for interacting with the platform. These are called like regular functions:

```jyro
# Get data
var org = GetOrganizationById(orgId)
var children = GetChildOrganizations(parentId)

# Perform actions
SendNotification(userId, title, message, type, link)
Log("Information", "Script completed")

# Create output
var notepadId = CreateNotepad({ data: results })
```

## Common Patterns

### Null Checking

```jyro
var org = GetOrganizationById(someId)
if org != null then
  Log("Information", "Found: " + org.name)
else
  Log("Warning", "Organization not found")
end
```

### String Concatenation

```jyro
var message = "Organization " + org.name + " has " + Length(children) + " children"
Log("Information", message)
```

### Working with Collections

```jyro
var orgs = GetAllOrganizations()
var activeCount = 0

foreach org in orgs do
  if org.isActive == true then
    activeCount = activeCount + 1
  end
end

Log("Information", "Active organizations: " + activeCount)
```

## Function Reference

For a complete list of available functions, see the [Iris API Functions Reference](/iris-api/functions/).

Key function categories include:

- **Organization Management**: `GetOrganizationById`, `CreateOrganization`, `GetChildOrganizations`
- **Person Management**: `GetPersonById`, `CreatePerson`, `GetPersonByEmail`
- **Facet Operations**: `GetFacetDefinitionByName`, `SetOrganizationFacetInstance`
- **Notifications**: `SendNotification`, `BroadcastNotification`
- **Logging & Output**: `Log`, `CreateNotepad`
- **Control Flow**: `CancelAction` (in Before hooks only)

## Next Steps

- [Data Context](data-context) - Understand what data is available to your scripts
- [Event Hooks](event-hooks) - React to domain events
- [Log Lines](log-lines) - Debug your scripts effectively
