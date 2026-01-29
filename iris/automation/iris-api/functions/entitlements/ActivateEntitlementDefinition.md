---
layout: default
title: ActivateEntitlementDefinition
parent: Iris API functions
has_children: false
has_toc: false
---

# ActivateEntitlementDefinition

Activates an entitlement definition, making it available for granting.

## Syntax

```jyro
ActivateEntitlementDefinition(entitlementDefinitionId)
```

## Parameters

- **entitlementDefinitionId** (string): The GUID of the entitlement definition to activate

## Returns

- **boolean**: true if activated successfully, false otherwise

## Description

Sets an entitlement definition's IsActive flag to true, making it available for granting to organizations. Newly created entitlement definitions are typically active by default, but you may need to reactivate one that was previously deactivated.

## Examples

```jyro
# Activate an entitlement definition
var success = ActivateEntitlementDefinition("9a862217-e2b6-4b31-a9c0-38de0d8f5e3b")

if success then
    Log("Information", "Entitlement definition is now active")
else
    Log("Error", "Failed to activate entitlement definition")
end
```

```jyro
# Activate by name
var entDef = GetEntitlementDefinitionByName("Premium Features")

if entDef != null then
    if not entDef.isActive then
        var activated = ActivateEntitlementDefinition(entDef.id)
        if activated then
            Log("Information", "Activated entitlement: " + entDef.name)
        end
    else
        Log("Information", "Entitlement is already active")
    end
end
```

```jyro
# Activate and then grant to organization
var entDef = GetEntitlementDefinitionByName("Beta Features")

if entDef != null then
    # Ensure it's active
    if not entDef.isActive then
        ActivateEntitlementDefinition(entDef.id)
    end

    # Now grant it
    var instanceId = AddEntitlementInstance(
        Data.Organization.Id,
        entDef.id,
        Today(),
        null,
        "Beta program enrollment"
    )

    if instanceId != null then
        Log("Information", "Organization enrolled in beta program")
    end
end
```

```jyro
# Batch activate all entitlements in a category
var allEntDefs = GetAllEntitlementDefinitions()

foreach entDef in allEntDefs do
    if entDef.category == "Feature" and not entDef.isActive then
        var activated = ActivateEntitlementDefinition(entDef.id)
        if activated then
            Log("Information", "Activated: " + entDef.name)
        end
    end
end
```

## See Also

- [DeactivateEntitlementDefinition](DeactivateEntitlementDefinition) - Deactivate a definition
- [CreateEntitlementDefinition](CreateEntitlementDefinition) - Create a definition
- [GetEntitlementDefinitionByName](GetEntitlementDefinitionByName) - Find definition by name
