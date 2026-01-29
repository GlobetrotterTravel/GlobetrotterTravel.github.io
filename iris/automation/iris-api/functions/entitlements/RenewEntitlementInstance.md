---
layout: default
title: RenewEntitlementInstance
parent: Iris API functions
has_children: false
has_toc: false
---

# RenewEntitlementInstance

Sets a new end date for an entitlement instance.

## Syntax

```jyro
RenewEntitlementInstance(organizationId, entitlementInstanceId, newEndDate)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **entitlementInstanceId** (string): The GUID of the entitlement instance to renew
- **newEndDate** (string or null): The new end date in ISO format "yyyy-MM-dd", or null for indefinite

## Returns

- **boolean**: true if renewed successfully, false otherwise

## Description

Updates the end date of an existing entitlement instance. Use this to:
- Extend a subscription or trial period
- Convert a time-limited entitlement to indefinite (by passing null)
- Set an earlier end date to expire an entitlement sooner

This is non-destructive - the entitlement instance is preserved with its original start date and notes.

## Examples

```jyro
# Extend entitlement by 1 year
var instance = GetOrganizationEntitlementInstanceByName(
    Data.Organization.Id,
    "Annual Subscription"
)

if instance != null then
    var newEndDate = DateAdd(instance.endDate, "years", 1)

    var renewed = RenewEntitlementInstance(
        Data.Organization.Id,
        instance.id,
        newEndDate
    )

    if renewed then
        Log("Information", "Subscription renewed until " + newEndDate)

        SendNotification(
            Data.Organization.PrimaryContact,
            "Subscription Renewed",
            "Your subscription has been renewed until " + newEndDate,
            "Success",
            "/account"
        )
    end
end
```

```jyro
# Convert trial to permanent
var instance = GetOrganizationEntitlementInstanceByName(
    Data.Organization.Id,
    "Trial Access"
)

if instance != null then
    # Set end date to null for indefinite access
    var converted = RenewEntitlementInstance(
        Data.Organization.Id,
        instance.id,
        null
    )

    if converted then
        Log("Information", "Trial converted to permanent access")
    end
end
```

```jyro
# Extend trial by 30 days
var instance = GetOrganizationEntitlementInstanceByName(
    Data.Organization.Id,
    "Premium Trial"
)

if instance != null then
    var currentEnd = instance.endDate
    var newEnd = DateAdd(currentEnd, "days", 30)

    RenewEntitlementInstance(Data.Organization.Id, instance.id, newEnd)
    Log("Information", "Trial extended from " + currentEnd + " to " + newEnd)
end
```

```jyro
# Set entitlement to expire soon (soft revoke)
var instance = GetOrganizationEntitlementInstanceByName(
    Data.Organization.Id,
    "Premium Features"
)

if instance != null then
    # Give 7 days notice before expiration
    var newEndDate = DateAdd(Today(), "days", 7)

    RenewEntitlementInstance(Data.Organization.Id, instance.id, newEndDate)

    SendNotification(
        Data.Organization.PrimaryContact,
        "Entitlement Expiring Soon",
        "Your Premium Features access will expire in 7 days.",
        "Warning",
        "/upgrade"
    )
end
```

```jyro
# Batch renewal of all expiring subscriptions
var allOrgs = GetAllOrganizations()
var renewalCount = 0

foreach org in allOrgs do
    var instance = GetOrganizationEntitlementInstanceByName(org.id, "Annual Subscription")

    if instance != null and instance.endDate != null then
        # Check if expiring in next 30 days
        var daysUntilExpiry = DateDiff(Today(), instance.endDate, "days")

        if daysUntilExpiry <= 30 and daysUntilExpiry >= 0 then
            var newEndDate = DateAdd(instance.endDate, "years", 1)
            var renewed = RenewEntitlementInstance(org.id, instance.id, newEndDate)

            if renewed then
                renewalCount = renewalCount + 1
                Log("Information", "Auto-renewed subscription for " + org.name)
            end
        end
    end
end

Log("Information", "Batch renewal complete: " + renewalCount + " subscriptions renewed")
```

## See Also

- [AddEntitlementInstance](AddEntitlementInstance.md) - Grant new entitlement
- [RemoveEntitlementInstance](RemoveEntitlementInstance.md) - Revoke entitlement
- [GetOrganizationEntitlementInstanceByName](GetOrganizationEntitlementInstanceByName.md) - Find instance by name
- [GetAllOrganizationEntitlementInstances](GetAllOrganizationEntitlementInstances.md) - List all instances
