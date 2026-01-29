---
layout: default
title: GetAllEntitlementInstances
parent: Iris API functions
has_children: false
has_toc: false
---

# GetAllEntitlementInstances

Retrieves all entitlement instances across all organizations in the tenant.

## Syntax

```jyro
GetAllEntitlementInstances(entitlementDefinitionName)
```

## Parameters

- **entitlementDefinitionName** (string, optional): The name of the entitlement definition to filter by (case-insensitive). Pass `null` or an empty string to retrieve all instances regardless of definition.

## Returns

- **array**: Array of entitlement instance objects with organization info, or empty array if none found

Each object contains:
- **id** (string): The GUID of the entitlement instance
- **entitlementDefinitionId** (string): The GUID of the entitlement definition
- **entitlementDefinitionName** (string): The name of the entitlement definition
- **organizationId** (string): The GUID of the organization that has this instance
- **organizationName** (string): The name of the organization
- **startDate** (string): The start date in ISO format "yyyy-MM-dd"
- **endDate** (string or null): The end date in ISO format, or null for indefinite
- **notes** (string or null): Notes about the instance
- **facetInstances** (array): Array of facet instances associated with this entitlement
  - **id** (string): The GUID of the facet instance
  - **facetDefinitionId** (string): The GUID of the facet definition
  - **facetDefinitionName** (string): The name of the facet definition
  - **values** (array): Array of string values for this facet

## Description

Returns all entitlement instances across all organizations in the tenant. This is useful for:
- Generating tenant-wide entitlement reports
- Finding all organizations with a specific entitlement
- Auditing entitlement usage across the tenant
- Identifying expiring entitlements for renewal notifications

Unlike `GetAllOrganizationEntitlementInstances` which retrieves instances for a single organization, this function retrieves instances globally across all organizations in the tenant.

## Examples

### Get All Entitlement Instances

```jyro
# Retrieve all entitlement instances in the tenant
var allInstances = GetAllEntitlementInstances(null)

if allInstances.length > 0 then
    Log("Information", "Found " + allInstances.length + " entitlement instances globally")

    foreach instance in allInstances do
        Log("Information", instance.organizationName + " has " + instance.entitlementDefinitionName)
        Log("Information", "  Started: " + instance.startDate)
        if instance.endDate != null then
            Log("Information", "  Expires: " + instance.endDate)
        else
            Log("Information", "  Never expires")
        end
    end
else
    Log("Information", "No entitlement instances found")
end
```

### Filter by Entitlement Type

```jyro
# Get all instances of a specific entitlement
var premiumInstances = GetAllEntitlementInstances("Premium Features")

Log("Information", premiumInstances.length + " organizations have Premium Features")

foreach instance in premiumInstances do
    Log("Information", "  - " + instance.organizationName)
end
```

### Find Expiring Entitlements

```jyro
# Find all entitlements expiring within 30 days
var allInstances = GetAllEntitlementInstances(null)
var today = Today()
var expiringInstances = []

foreach instance in allInstances do
    if instance.endDate != null then
        var daysUntilExpiry = DateDiff(today, instance.endDate, "days")
        if daysUntilExpiry >= 0 and daysUntilExpiry <= 30 then
            expiringInstances.add(instance)
        end
    end
end

if expiringInstances.length > 0 then
    Log("Warning", expiringInstances.length + " entitlements expiring within 30 days:")

    foreach instance in expiringInstances do
        Log("Warning", "  " + instance.organizationName + " - " + instance.entitlementDefinitionName + " expires " + instance.endDate)
    end

    # Optionally send notifications
    foreach instance in expiringInstances do
        BroadcastNotification(
            "Entitlement Expiring Soon",
            instance.organizationName + "'s " + instance.entitlementDefinitionName + " expires on " + instance.endDate,
            "Warning",
            "/organizations/" + instance.organizationId,
            true
        )
    end
else
    Log("Information", "No entitlements expiring within 30 days")
end
```

### Generate Entitlement Summary Report

```jyro
# Generate a summary report of entitlement distribution
var allInstances = GetAllEntitlementInstances(null)

# Count by entitlement type
var countByType = {}
foreach instance in allInstances do
    var name = instance.entitlementDefinitionName
    if countByType[name] == null then
        countByType[name] = 0
    end
    countByType[name] = countByType[name] + 1
end

# Build report
var report = "Entitlement Distribution Report\n"
report = report + "Generated: " + Now() + "\n\n"
report = report + "Total Instances: " + allInstances.length + "\n\n"

foreach key in Keys(countByType) do
    report = report + key + ": " + countByType[key] + " organizations\n"
end

Log("Information", report)

# Optionally save to notepad
CreateNotepad("EntitlementReport", "text/plain", report)
```

### Count Organizations by Entitlement Status

```jyro
# Categorize organizations by their entitlement status
var allInstances = GetAllEntitlementInstances(null)
var today = Today()

var activeCount = 0
var expiringCount = 0
var expiredCount = 0
var indefiniteCount = 0

foreach instance in allInstances do
    if instance.endDate == null then
        indefiniteCount = indefiniteCount + 1
    else
        var daysUntilExpiry = DateDiff(today, instance.endDate, "days")
        if daysUntilExpiry < 0 then
            expiredCount = expiredCount + 1
        elseif daysUntilExpiry <= 30 then
            expiringCount = expiringCount + 1
        else
            activeCount = activeCount + 1
        end
    end
end

Log("Information", "Entitlement Status Summary:")
Log("Information", "  Active (>30 days): " + activeCount)
Log("Information", "  Expiring Soon (<=30 days): " + expiringCount)
Log("Information", "  Expired: " + expiredCount)
Log("Information", "  Indefinite: " + indefiniteCount)
```

### Check for Organizations Missing a Required Entitlement

```jyro
# Find organizations that don't have a required entitlement
var requiredEntitlement = "Base License"
var allOrgs = GetAllOrganizations()
var licensedInstances = GetAllEntitlementInstances(requiredEntitlement)

# Build set of licensed org IDs
var licensedOrgIds = {}
foreach instance in licensedInstances do
    licensedOrgIds[instance.organizationId] = true
end

# Find unlicensed orgs
var unlicensedOrgs = []
foreach org in allOrgs do
    if licensedOrgIds[org.id] != true then
        unlicensedOrgs.add(org)
    end
end

if unlicensedOrgs.length > 0 then
    Log("Warning", unlicensedOrgs.length + " organizations missing " + requiredEntitlement + ":")
    foreach org in unlicensedOrgs do
        Log("Warning", "  - " + org.name)
    end
else
    Log("Information", "All organizations have " + requiredEntitlement)
end
```

### Working with Facet Instances

```jyro
# Get all entitlements and display their facet values
var allInstances = GetAllEntitlementInstances("SharePoint Integration")

foreach instance in allInstances do
    Log("Information", "Organization: " + instance.organizationName)
    Log("Information", "  Entitlement: " + instance.entitlementDefinitionName)

    # Check if there are facet instances
    if instance.facetInstances != null and instance.facetInstances.length > 0 then
        Log("Information", "  Facet values:")

        foreach facet in instance.facetInstances do
            # Use facetDefinitionName for display (no lookup needed!)
            Log("Information", "    " + facet.facetDefinitionName + ":")

            # Display all values for this facet
            foreach value in facet.values do
                Log("Information", "      - " + value)
            end
        end
    else
        Log("Information", "  No facet values configured")
    end
end
```

```jyro
# Find entitlements with specific facet values by name
var allInstances = GetAllEntitlementInstances(null)

foreach instance in allInstances do
    foreach facet in instance.facetInstances do
        # Use facetDefinitionName directly instead of looking up by ID
        if facet.facetDefinitionName == "SharePoint Site URL" then
            foreach value in facet.values do
                if value.contains("contoso.sharepoint.com") then
                    Log("Information", instance.organizationName + " uses Contoso SharePoint: " + value)
                end
            end
        end
    end
end
```

## See Also

- [GetAllOrganizationEntitlementInstances](GetAllOrganizationEntitlementInstances.md) - Get instances for a single organization
- [GetOrganizationEntitlementInstanceByName](GetOrganizationEntitlementInstanceByName.md) - Find single instance by name
- [GetAllEntitlementDefinitions](GetAllEntitlementDefinitions.md) - Get all entitlement definitions
- [AddEntitlementInstance](AddEntitlementInstance.md) - Grant new entitlement
- [RemoveEntitlementInstance](RemoveEntitlementInstance.md) - Revoke entitlement
- [RenewEntitlementInstance](RenewEntitlementInstance.md) - Extend entitlement expiry
