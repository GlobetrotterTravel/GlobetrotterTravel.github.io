---
layout: default
title: GetAllEntitlementDefinitions
parent: Iris API functions
has_children: false
has_toc: false
---

# GetAllEntitlementDefinitions

Retrieves all entitlement definitions in the tenant.

## Syntax

```jyro
GetAllEntitlementDefinitions()
```

## Parameters

None

## Returns

- **array**: Array of all entitlement definition objects, or empty array if error

Each object contains:
- **id** (string): The GUID of the entitlement definition
- **name** (string): The name of the entitlement
- **category** (string): The logical category
- **description** (string): The description
- **isActive** (boolean): Whether the definition is active
- **dataSensitivity** (string): The data sensitivity level
- **allowMultipleInstances** (boolean): Whether multiple instances can be granted to an organization
- **defaultTerm** (number or null): Default duration in days, or null for indefinite

## Description

Returns an array containing all entitlement definitions in the tenant. Use this to discover available entitlements or build lookup maps for scripting.

## Examples

```jyro
# List all entitlement definitions
var allEntDefs = GetAllEntitlementDefinitions()
Log("Information", "Found " + allEntDefs.length + " entitlement definitions")

foreach entDef in allEntDefs do
    Log("Information", "Entitlement: " + entDef.name + " (Active: " + entDef.isActive + ")")
end
```

```jyro
# Filter to active entitlements only
var allEntDefs = GetAllEntitlementDefinitions()
var activeEntitlements = []

foreach entDef in allEntDefs do
    if entDef.isActive then
        activeEntitlements.add(entDef)
    end
end

Log("Information", "Active entitlements: " + activeEntitlements.length)
```

```jyro
# Build entitlement name lookup map
var allEntDefs = GetAllEntitlementDefinitions()
var entNameMap = {}

foreach entDef in allEntDefs do
    entNameMap[entDef.name] = entDef.id
end

# Use the map to add an entitlement instance
if entNameMap["Premium Features"] != null then
    AddEntitlementInstance(
        Data.Organization.Id,
        entNameMap["Premium Features"],
        Today(),
        null,
        "Granted via automation"
    )
end
```

```jyro
# Find entitlements by category
var allEntDefs = GetAllEntitlementDefinitions()

foreach entDef in allEntDefs do
    if entDef.category == "Subscription" then
        Log("Information", "Subscription entitlement: " + entDef.name)
        if entDef.defaultTerm != null then
            Log("Information", "  Default term: " + entDef.defaultTerm + " days")
        else
            Log("Information", "  Default term: Indefinite")
        end
    end
end
```

## See Also

- [GetEntitlementDefinitionByName](GetEntitlementDefinitionByName.md) - Get single definition by name
- [CreateEntitlementDefinition](CreateEntitlementDefinition.md) - Create new definition
- [AddEntitlementInstance](AddEntitlementInstance.md) - Grant entitlement to organization
- [GetAllEntitlementInstances](GetAllEntitlementInstances.md) - Get all instances across all organizations
- [GetAllOrganizationEntitlementInstances](GetAllOrganizationEntitlementInstances.md) - Get instances for a single organization
