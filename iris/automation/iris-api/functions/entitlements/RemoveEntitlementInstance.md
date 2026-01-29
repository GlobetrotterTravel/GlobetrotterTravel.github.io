---
layout: default
title: RemoveEntitlementInstance
parent: Iris API functions
has_children: false
has_toc: false
---

# RemoveEntitlementInstance

Removes an entitlement instance from an organization.

## Syntax

```jyro
RemoveEntitlementInstance(organizationId, entitlementInstanceId)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **entitlementInstanceId** (string): The GUID of the entitlement instance to remove

## Returns

- **boolean**: true if removed successfully, false otherwise

## Description

Revokes an entitlement from an organization by removing the entitlement instance. This is a permanent operation - the entitlement is immediately revoked. For time-limited entitlements, consider using RenewEntitlementInstance to set an earlier end date instead of immediate removal.

## Examples

```jyro
# Remove an entitlement instance by ID
var success = RemoveEntitlementInstance(
    Data.Organization.Id,
    "abc12345-1234-5678-9012-abcdef012345"
)

if success then
    Log("Information", "Entitlement revoked successfully")
else
    Log("Error", "Failed to revoke entitlement")
end
```

```jyro
# Remove an entitlement by name
var instance = GetOrganizationEntitlementInstanceByName(
    Data.Organization.Id,
    "Premium Features"
)

if instance != null then
    var removed = RemoveEntitlementInstance(
        Data.Organization.Id,
        instance.id
    )

    if removed then
        Log("Information", "Premium Features entitlement revoked")

        SendNotification(
            Data.Organization.PrimaryContact,
            "Entitlement Revoked",
            "Your Premium Features access has been revoked.",
            "Warning",
            "/billing"
        )
    end
else
    Log("Information", "Organization does not have Premium Features")
end
```

```jyro
# Remove all instances of a specific entitlement type
var trialDef = GetEntitlementDefinitionByName("Trial Access")
var instances = GetAllOrganizationEntitlementInstances(Data.Organization.Id)
var removedCount = 0

foreach instance in instances do
    if instance.entitlementDefinitionId == trialDef.id then
        var removed = RemoveEntitlementInstance(
            Data.Organization.Id,
            instance.id
        )
        if removed then
            removedCount = removedCount + 1
        end
    end
end

Log("Information", "Removed " + removedCount + " trial entitlements")
```

```jyro
# Remove expired entitlements
var instances = GetAllOrganizationEntitlementInstances(Data.Organization.Id)
var today = Today()

foreach instance in instances do
    if instance.endDate != null and instance.endDate < today then
        var removed = RemoveEntitlementInstance(
            Data.Organization.Id,
            instance.id
        )
        if removed then
            Log("Information", "Removed expired entitlement: " + instance.id)
        end
    end
end
```

```jyro
# Revoke entitlement on organization deletion
BeforeOrganizationDeleted:
    var instances = GetAllOrganizationEntitlementInstances(Data.Organization.Id)

    foreach instance in instances do
        RemoveEntitlementInstance(Data.Organization.Id, instance.id)
        Log("Information", "Revoked entitlement before deletion: " + instance.id)
    end
```

## See Also

- [AddEntitlementInstance](AddEntitlementInstance) - Grant an entitlement
- [RenewEntitlementInstance](RenewEntitlementInstance) - Modify expiration date
- [GetAllOrganizationEntitlementInstances](GetAllOrganizationEntitlementInstances) - List all instances
- [GetOrganizationEntitlementInstanceByName](GetOrganizationEntitlementInstanceByName) - Find instance by name
