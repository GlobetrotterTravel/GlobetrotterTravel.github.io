---
layout: default
title: DeleteEntitlementDefinition
parent: Iris API functions
has_children: false
has_toc: false
---

# DeleteEntitlementDefinition

Deletes an entitlement definition from the tenant.

## Syntax

```jyro
DeleteEntitlementDefinition(entitlementDefinitionId)
```

## Parameters

- **entitlementDefinitionId** (string): The GUID of the entitlement definition to delete

## Returns

- **boolean**: true if deleted successfully, false otherwise

## Description

Permanently removes an entitlement definition from the tenant. Use with caution - this operation cannot be undone. Consider using DeactivateEntitlementDefinition instead if you want to preserve the definition but prevent new instances from being granted.

**Warning**: Deleting an entitlement definition does not automatically remove existing entitlement instances from organizations. Those instances will become orphaned.

## Examples

```jyro
# Delete an entitlement definition by ID
var success = DeleteEntitlementDefinition("9a862217-e2b6-4b31-a9c0-38de0d8f5e3b")

if success then
    Log("Information", "Entitlement definition deleted")
else
    Log("Error", "Failed to delete entitlement definition")
end
```

```jyro
# Delete an entitlement definition by name
var entDef = GetEntitlementDefinitionByName("Deprecated Feature")

if entDef != null then
    var deleted = DeleteEntitlementDefinition(entDef.id)
    if deleted then
        Log("Information", "Deleted entitlement: " + entDef.name)
    else
        Log("Error", "Failed to delete entitlement: " + entDef.name)
    end
else
    Log("Warning", "Entitlement definition not found")
end
```

```jyro
# Safely delete with confirmation check
var entDef = GetEntitlementDefinitionByName("Old Trial")

if entDef != null then
    # First deactivate to prevent new grants
    DeactivateEntitlementDefinition(entDef.id)

    # Log for audit trail
    Log("Warning", "Deleting entitlement definition: " + entDef.name + " (ID: " + entDef.id + ")")

    # Then delete
    var deleted = DeleteEntitlementDefinition(entDef.id)
    if deleted then
        Log("Information", "Entitlement definition permanently deleted")
    end
end
```

## See Also

- [CreateEntitlementDefinition](CreateEntitlementDefinition.md) - Create a definition
- [DeactivateEntitlementDefinition](DeactivateEntitlementDefinition.md) - Deactivate instead of delete
- [GetEntitlementDefinitionByName](GetEntitlementDefinitionByName.md) - Find definition by name
