---
layout: default
title: DeactivateEntitlementDefinition
parent: Iris API functions
has_children: false
has_toc: false
---

# DeactivateEntitlementDefinition

Deactivates an entitlement definition, preventing new grants.

## Syntax

```jyro
DeactivateEntitlementDefinition(entitlementDefinitionId)
```

## Parameters

- **entitlementDefinitionId** (string): The GUID of the entitlement definition to deactivate

## Returns

- **boolean**: true if deactivated successfully, false otherwise

## Description

Sets an entitlement definition's IsActive flag to false, preventing new entitlement instances from being granted to organizations. Existing entitlement instances are not affected - organizations that already have this entitlement will retain it.

Use this instead of DeleteEntitlementDefinition when you want to stop granting an entitlement but preserve the definition and existing grants.

## Examples

```jyro
# Deactivate an entitlement definition
var success = DeactivateEntitlementDefinition("9a862217-e2b6-4b31-a9c0-38de0d8f5e3b")

if success then
    Log("Information", "Entitlement definition is now inactive")
else
    Log("Error", "Failed to deactivate entitlement definition")
end
```

```jyro
# Deactivate by name
var entDef = GetEntitlementDefinitionByName("Legacy Plan")

if entDef != null then
    if entDef.isActive then
        var deactivated = DeactivateEntitlementDefinition(entDef.id)
        if deactivated then
            Log("Information", "Deactivated entitlement: " + entDef.name)
        end
    else
        Log("Information", "Entitlement is already inactive")
    end
end
```

```jyro
# Deactivate trial after promotion ends
var trialDef = GetEntitlementDefinitionByName("Summer Promotion Trial")

if trialDef != null and trialDef.isActive then
    DeactivateEntitlementDefinition(trialDef.id)
    Log("Information", "Summer promotion trial has ended")

    # Notify admins
    BroadcastNotification(
        "Promotion Ended",
        "The Summer Promotion Trial entitlement is no longer available for new grants.",
        "Info",
        null,
        true
    )
end
```

```jyro
# Sunset a feature by deactivating its entitlement
var featureDef = GetEntitlementDefinitionByName("Deprecated Feature X")

if featureDef != null then
    # Deactivate to prevent new grants
    DeactivateEntitlementDefinition(featureDef.id)

    # Log which organizations still have it
    var allOrgs = GetAllOrganizations()
    foreach org in allOrgs do
        var instance = GetOrganizationEntitlementInstanceByName(org.id, "Deprecated Feature X")
        if instance != null then
            Log("Information", "Organization " + org.name + " still has deprecated feature")
        end
    end
end
```

## See Also

- [ActivateEntitlementDefinition](ActivateEntitlementDefinition.md) - Reactivate a definition
- [DeleteEntitlementDefinition](DeleteEntitlementDefinition.md) - Permanently delete
- [GetEntitlementDefinitionByName](GetEntitlementDefinitionByName.md) - Find definition by name
