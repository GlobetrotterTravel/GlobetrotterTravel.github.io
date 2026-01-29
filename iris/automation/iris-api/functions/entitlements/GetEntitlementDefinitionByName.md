---
layout: default
title: GetEntitlementDefinitionByName
parent: Iris API functions
has_children: false
has_toc: false
---

# GetEntitlementDefinitionByName

Retrieves an entitlement definition by its name (case-insensitive).

## Syntax

```jyro
GetEntitlementDefinitionByName(name)
```

## Parameters

- **name** (string): The name of the entitlement definition to retrieve

## Returns

- **object**: Entitlement definition object, or null if not found

The returned object contains:
- **id** (string): The GUID of the entitlement definition
- **name** (string): The name of the entitlement
- **category** (string): The logical category
- **description** (string): The description
- **isActive** (boolean): Whether the definition is active
- **dataSensitivity** (string): The data sensitivity level
- **allowMultipleInstances** (boolean): Whether multiple instances can be granted
- **defaultTerm** (number or null): Default duration in days, or null for indefinite

## Description

Looks up an entitlement definition by name using case-insensitive matching. This is useful for getting the entitlement definition ID without hardcoding GUIDs in scripts.

## Examples

```jyro
# Get entitlement definition and use its ID
var premiumDef = GetEntitlementDefinitionByName("Premium Features")
if premiumDef != null then
    Log("Information", "Found entitlement: " + premiumDef.id)

    # Grant the entitlement to an organization
    var instanceId = AddEntitlementInstance(
        Data.Organization.Id,
        premiumDef.id,
        Today(),
        null,
        "Granted via automation"
    )

    if instanceId != null then
        Log("Information", "Entitlement granted: " + instanceId)
    end
else
    Log("Warning", "Entitlement definition 'Premium Features' not found")
end
```

```jyro
# Check if entitlement allows multiple instances
var trialDef = GetEntitlementDefinitionByName("Trial Access")
if trialDef != null then
    if trialDef.allowMultipleInstances then
        Log("Information", "Trial Access can be granted multiple times")
    else
        Log("Information", "Trial Access is single-instance only")
    end
end
```

```jyro
# Check default term for an entitlement
var subscriptionDef = GetEntitlementDefinitionByName("Annual Subscription")
if subscriptionDef != null then
    if subscriptionDef.defaultTerm != null then
        Log("Information", "Default subscription term: " + subscriptionDef.defaultTerm + " days")
    else
        Log("Information", "Subscription has no default term (indefinite)")
    end
end
```

```jyro
# Validate entitlement definition exists before use
var entDef = GetEntitlementDefinitionByName("Enterprise License")
if entDef == null then
    Log("Error", "Required entitlement definition 'Enterprise License' not found")
    CancelAction("Missing required entitlement definition")
end

if not entDef.isActive then
    Log("Warning", "Entitlement 'Enterprise License' is not active")
end
```

## See Also

- [GetAllEntitlementDefinitions](GetAllEntitlementDefinitions) - Get all definitions
- [CreateEntitlementDefinition](CreateEntitlementDefinition) - Create new definition
- [AddEntitlementInstance](AddEntitlementInstance) - Grant entitlement to organization
