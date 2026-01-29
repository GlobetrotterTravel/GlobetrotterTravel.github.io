---
layout: default
title: GetOrganizationEntitlementInstanceByName
parent: Iris API functions
has_children: false
has_toc: false
---

# GetOrganizationEntitlementInstanceByName

Retrieves an entitlement instance from an organization by entitlement definition name.

## Syntax

```jyro
GetOrganizationEntitlementInstanceByName(organizationId, entitlementDefinitionName)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **entitlementDefinitionName** (string): The name of the entitlement definition (case-insensitive)

## Returns

- **object**: Entitlement instance object, or null if not found

The returned object contains:
- **id** (string): The GUID of the entitlement instance
- **entitlementDefinitionId** (string): The GUID of the entitlement definition
- **startDate** (string): The start date in ISO format "yyyy-MM-dd"
- **endDate** (string or null): The end date in ISO format, or null for indefinite
- **notes** (string or null): Notes about the instance
- **facetInstances** (array): Array of facet instances associated with this entitlement
  - **id** (string): The GUID of the facet instance
  - **facetDefinitionId** (string): The GUID of the facet definition
  - **facetDefinitionName** (string): The name of the facet definition
  - **values** (array): Array of string values for this facet

## Description

Finds an entitlement instance by the name of its entitlement definition. This is a convenient way to check if an organization has a specific entitlement without knowing the definition's GUID.

**Note**: If the organization has multiple instances of the same entitlement definition (when AllowMultipleInstances is true), this function returns the first match. Use GetAllOrganizationEntitlementInstances if you need all instances.

## Examples

```jyro
# Check if organization has Premium Features
var instance = GetOrganizationEntitlementInstanceByName(
    Data.Organization.Id,
    "Premium Features"
)

if instance != null then
    Log("Information", "Organization has Premium Features")
    Log("Information", "Started: " + instance.startDate)

    if instance.endDate != null then
        Log("Information", "Expires: " + instance.endDate)
    else
        Log("Information", "Never expires")
    end
else
    Log("Information", "Organization does not have Premium Features")
end
```

```jyro
# Renew an entitlement by name
var instance = GetOrganizationEntitlementInstanceByName(
    Data.Organization.Id,
    "Annual Subscription"
)

if instance != null then
    var newEndDate = DateAdd(Today(), "years", 1)
    var renewed = RenewEntitlementInstance(
        Data.Organization.Id,
        instance.id,
        newEndDate
    )

    if renewed then
        Log("Information", "Subscription renewed until " + newEndDate)
    end
else
    Log("Warning", "Organization does not have Annual Subscription")
end
```

```jyro
# Check trial status and convert if needed
var trialInstance = GetOrganizationEntitlementInstanceByName(
    Data.Organization.Id,
    "Trial Access"
)

if trialInstance != null then
    if trialInstance.endDate != null then
        var daysLeft = DateDiff(Today(), trialInstance.endDate, "days")

        if daysLeft <= 0 then
            Log("Information", "Trial has expired")
        else if daysLeft <= 7 then
            Log("Warning", "Trial expires in " + daysLeft + " days")

            SendNotification(
                Data.Organization.PrimaryContact,
                "Trial Expiring Soon",
                "Your trial expires in " + daysLeft + " days. Upgrade now!",
                "Warning",
                "/upgrade"
            )
        end
    end
end
```

```jyro
# Remove an entitlement by name
var instance = GetOrganizationEntitlementInstanceByName(
    Data.Organization.Id,
    "Beta Features"
)

if instance != null then
    var removed = RemoveEntitlementInstance(
        Data.Organization.Id,
        instance.id
    )

    if removed then
        Log("Information", "Beta Features access revoked")
    end
end
```

```jyro
# Gate a feature based on entitlement
var premiumInstance = GetOrganizationEntitlementInstanceByName(
    Data.Organization.Id,
    "Premium Features"
)

if premiumInstance == null then
    Log("Warning", "Organization attempted to use premium feature without entitlement")
    CancelAction("Premium Features required for this operation")
end

# Check if not expired
if premiumInstance.endDate != null then
    if premiumInstance.endDate < Today() then
        Log("Warning", "Premium entitlement has expired")
        CancelAction("Premium Features entitlement has expired")
    end
end

# Feature is available, proceed
Log("Information", "Premium feature access granted")
```

```jyro
# Check multiple entitlements
var hasPremium = GetOrganizationEntitlementInstanceByName(orgId, "Premium Features") != null
var hasEnterprise = GetOrganizationEntitlementInstanceByName(orgId, "Enterprise License") != null
var hasTrial = GetOrganizationEntitlementInstanceByName(orgId, "Trial Access") != null

if hasEnterprise then
    Log("Information", "Enterprise tier")
else if hasPremium then
    Log("Information", "Premium tier")
else if hasTrial then
    Log("Information", "Trial tier")
else
    Log("Information", "Free tier")
end
```

```jyro
# Access facet values for an entitlement instance
var instance = GetOrganizationEntitlementInstanceByName(
    Data.Organization.Id,
    "SharePoint Integration"
)

if instance != null then
    Log("Information", "Organization has SharePoint Integration")

    # Access facet values
    if instance.facetInstances != null and instance.facetInstances.length > 0 then
        foreach facet in instance.facetInstances do
            # Use facetDefinitionName for display (no lookup needed!)
            Log("Information", "  Facet: " + facet.facetDefinitionName)

            foreach value in facet.values do
                Log("Information", "    Value: " + value)
            end
        end
    end
end
```

## See Also

- [GetAllOrganizationEntitlementInstances](GetAllOrganizationEntitlementInstances.md) - Get all instances for an organization
- [GetAllEntitlementInstances](GetAllEntitlementInstances.md) - Get all instances across all organizations
- [GetEntitlementDefinitionByName](GetEntitlementDefinitionByName.md) - Get definition by name
- [AddEntitlementInstance](AddEntitlementInstance.md) - Grant new entitlement
- [RemoveEntitlementInstance](RemoveEntitlementInstance.md) - Revoke entitlement
- [RenewEntitlementInstance](RenewEntitlementInstance.md) - Extend expiration
