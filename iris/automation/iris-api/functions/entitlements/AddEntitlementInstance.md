---
layout: default
title: AddEntitlementInstance
parent: Iris API functions
has_children: false
has_toc: false
---

# AddEntitlementInstance

Adds an entitlement instance to an organization.

## Syntax

```jyro
AddEntitlementInstance(organizationId, entitlementDefinitionId, startDate, endDate, notes)
```

## Parameters

- **organizationId** (string): The GUID of the organization to grant the entitlement to
- **entitlementDefinitionId** (string): The GUID of the entitlement definition to grant
- **startDate** (string): The start date in ISO format "yyyy-MM-dd"
- **endDate** (string or null): Optional end date in ISO format "yyyy-MM-dd", or null for indefinite
- **notes** (string or null): Optional notes about the entitlement instance

## Returns

- **string**: The GUID of the newly created entitlement instance, or null if creation failed

## Description

Grants an entitlement to an organization by creating a new entitlement instance. The instance tracks when the entitlement was granted, when it expires (if applicable), and any notes about the grant.

If the entitlement definition has `allowMultipleInstances` set to false, attempting to add a second instance will fail.

## Examples

```jyro
# Grant indefinite entitlement
var instanceId = AddEntitlementInstance(
    Data.Organization.Id,
    "9a862217-e2b6-4b31-a9c0-38de0d8f5e3b",
    "2025-01-01",
    null,
    "Granted via automation"
)

if instanceId != null then
    Log("Information", "Entitlement granted: " + instanceId)
else
    Log("Error", "Failed to grant entitlement")
end
```

```jyro
# Grant time-limited trial
var premiumDef = GetEntitlementDefinitionByName("Premium Features")

if premiumDef != null then
    var startDate = Today()
    var endDate = DateAdd(Today(), "days", 30)

    var instanceId = AddEntitlementInstance(
        Data.Organization.Id,
        premiumDef.id,
        startDate,
        endDate,
        "30-day trial"
    )

    if instanceId != null then
        Log("Information", "Trial started, expires: " + endDate)
        SendNotification(
            Data.Organization.PrimaryContact,
            "Trial Started",
            "Your 30-day Premium trial has started!",
            "Success",
            "/features"
        )
    end
end
```

```jyro
# Grant entitlement to new organization on creation
BeforeOrganizationCreated:
    var starterDef = GetEntitlementDefinitionByName("Starter Plan")

    if starterDef != null then
        AddEntitlementInstance(
            Data.Organization.Id,
            starterDef.id,
            Today(),
            null,
            "Default starter plan for new organizations"
        )
    end
```

```jyro
# Grant annual subscription
var subscriptionDef = GetEntitlementDefinitionByName("Annual Subscription")

if subscriptionDef != null then
    var startDate = Today()
    var endDate = DateAdd(Today(), "years", 1)

    var instanceId = AddEntitlementInstance(
        Data.Organization.Id,
        subscriptionDef.id,
        startDate,
        endDate,
        "Annual subscription - Invoice #12345"
    )

    if instanceId != null then
        Log("Information", "Subscription granted until " + endDate)
    end
end
```

```jyro
# Grant multiple seat licenses
var seatDef = GetEntitlementDefinitionByName("User Seat License")

if seatDef != null and seatDef.allowMultipleInstances then
    var seatCount = 10

    for i = 1 to seatCount do
        var instanceId = AddEntitlementInstance(
            Data.Organization.Id,
            seatDef.id,
            Today(),
            null,
            "Seat license " + i + " of " + seatCount
        )

        if instanceId != null then
            Log("Information", "Granted seat " + i)
        end
    end

    Log("Information", "Granted " + seatCount + " seat licenses")
end
```

## See Also

- [RemoveEntitlementInstance](RemoveEntitlementInstance.md) - Revoke an entitlement
- [RenewEntitlementInstance](RenewEntitlementInstance.md) - Extend expiration date
- [GetAllOrganizationEntitlementInstances](GetAllOrganizationEntitlementInstances.md) - List all instances
- [GetEntitlementDefinitionByName](GetEntitlementDefinitionByName.md) - Find definition by name
