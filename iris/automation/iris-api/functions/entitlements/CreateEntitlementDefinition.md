---
layout: default
title: CreateEntitlementDefinition
parent: Iris API functions
has_children: false
has_toc: false
---

# CreateEntitlementDefinition

Creates a new entitlement definition in the tenant.

## Syntax

```jyro
CreateEntitlementDefinition(category, name, description, dataSensitivity, options)
```

## Parameters

- **category** (string): The logical category for the entitlement (e.g., "Subscription", "Feature", "License")
- **name** (string): The unique name of the entitlement
- **description** (string): A description of what the entitlement grants
- **dataSensitivity** (string): The data sensitivity level - "None", "Low", "Medium", "High", or "Critical"
- **options** (object, optional): Additional configuration options:
  - **allowMultipleInstances** (boolean): Whether the entitlement can be granted multiple times to the same organization (default: false)
  - **defaultTerm** (number or null): Default duration in days, or null for indefinite (default: null)

## Returns

- **string**: The GUID of the newly created entitlement definition, or null if creation failed

## Description

Creates a new entitlement definition that can be granted to organizations. Entitlement definitions describe something of value (features, licenses, subscriptions) that organizations can receive.

## Examples

```jyro
# Create a simple feature entitlement
var entId = CreateEntitlementDefinition(
    "Feature",
    "Advanced Analytics",
    "Access to advanced analytics dashboard and reports",
    "Low",
    null
)

if entId != null then
    Log("Information", "Created entitlement definition: " + entId)
else
    Log("Error", "Failed to create entitlement definition")
end
```

```jyro
# Create a subscription entitlement with default term
var entId = CreateEntitlementDefinition(
    "Subscription",
    "Premium Plan",
    "Premium subscription with full feature access",
    "Medium",
    {
        "allowMultipleInstances": false,
        "defaultTerm": 365
    }
)

if entId != null then
    Log("Information", "Created Premium Plan entitlement: " + entId)
end
```

```jyro
# Create an entitlement that allows multiple instances
var entId = CreateEntitlementDefinition(
    "License",
    "User Seat License",
    "License for an additional user seat",
    "Low",
    {
        "allowMultipleInstances": true,
        "defaultTerm": null
    }
)

if entId != null then
    Log("Information", "Created seat license entitlement: " + entId)

    # Immediately grant 5 seats to an organization
    for i = 1 to 5 do
        AddEntitlementInstance(
            Data.Organization.Id,
            entId,
            Today(),
            null,
            "Seat " + i
        )
    end
end
```

```jyro
# Create trial entitlement with 30-day default
var trialId = CreateEntitlementDefinition(
    "Trial",
    "30-Day Trial",
    "Trial access to all premium features",
    "None",
    {
        "allowMultipleInstances": false,
        "defaultTerm": 30
    }
)

if trialId != null then
    Log("Information", "Created trial entitlement")

    # Grant trial to new organization
    var endDate = DateAdd(Today(), "days", 30)
    AddEntitlementInstance(
        Data.Organization.Id,
        trialId,
        Today(),
        endDate,
        "Welcome trial"
    )
end
```

## See Also

- [GetAllEntitlementDefinitions](GetAllEntitlementDefinitions.md) - Get all definitions
- [DeleteEntitlementDefinition](DeleteEntitlementDefinition.md) - Delete a definition
- [ActivateEntitlementDefinition](ActivateEntitlementDefinition.md) - Activate a definition
- [AddEntitlementInstance](AddEntitlementInstance.md) - Grant entitlement to organization
