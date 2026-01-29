---
layout: default
title: GetPersonsBySet
parent: Iris API functions
has_children: false
has_toc: false
---

# GetPersonsBySet

Retrieves all persons matching a specific set definition.

## Syntax

```jyro
GetPersonsBySet(setName)
```

## Parameters

- **setName** (string): The name of the set definition to match

## Returns

- **array**: Array of Person objects matching the set criteria
- **array** (empty): If the set exists but no persons match
- **null**: If the set is not found or targets the wrong resource type (not Person)

## Description

Evaluates a set definition against all persons in the tenant and returns those that match the set's criteria. Set definitions are pre-configured rules that can filter persons based on various conditions like facet values, field values, or other properties.

This is useful for targeting specific groups of persons for notifications, reports, or bulk operations.

## Examples

```jyro
# Get all active employees
var activeEmployees = GetPersonsBySet("Active Employees")

if activeEmployees == null then
    Log("Warning", "Set not found or targets wrong resource type")
else if Length(activeEmployees) == 0 then
    Log("Information", "No persons match this set")
else
    Log("Information", "Found " + Length(activeEmployees) + " active employees")

    foreach person in activeEmployees do
        Log("Information", "  - " + person.firstName + " " + person.lastName)
    end
end
```

```jyro
# Send notification to VIP members
var vipMembers = GetPersonsBySet("VIP Members")

if vipMembers != null and Length(vipMembers) > 0 then
    foreach person in vipMembers do
        SendNotification(
            person.emailAddress,
            "VIP Exclusive Offer",
            "As a VIP member, you have early access to our new features!",
            "Info",
            "/vip-offers"
        )
    end

    Log("Information", "Sent notifications to " + Length(vipMembers) + " VIP members")
end
```

```jyro
# Compare two sets
var managers = GetPersonsBySet("Managers")
var executives = GetPersonsBySet("Executives")

if managers != null and executives != null then
    Log("Information", "Managers: " + Length(managers))
    Log("Information", "Executives: " + Length(executives))
end
```

```jyro
# Use set for conditional logic in event hook
AfterPersonCreated:
    var newPerson = Data.Person
    var vipDomains = GetPersonsBySet("VIP Email Domains")

    # Check if new person's domain qualifies for VIP
    var isVip = false
    foreach vipPerson in vipDomains do
        if EndsWith(newPerson.emailAddress, vipPerson.emailDomain) then
            isVip = true
        end
    end

    if isVip then
        Log("Information", "New VIP person registered: " + newPerson.emailAddress)
    end
```
