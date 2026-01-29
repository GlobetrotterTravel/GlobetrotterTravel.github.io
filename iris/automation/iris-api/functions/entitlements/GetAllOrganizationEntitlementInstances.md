---
layout: default
title: GetAllOrganizationEntitlementInstances
parent: Iris API functions
has_children: false
has_toc: false
---

# GetAllOrganizationEntitlementInstances

Retrieves all entitlement instances from an organization.

## Syntax

```jyro
GetAllOrganizationEntitlementInstances(organizationId)
```

## Parameters

- **organizationId** (string): The GUID of the organization

## Returns

- **array**: Array of entitlement instance objects, or empty array if none found

Each object contains:
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

Returns all entitlement instances currently assigned to an organization. Use this to audit what entitlements an organization has, check for expiring entitlements, or perform batch operations.

## Examples

```jyro
# List all entitlements for an organization
var instances = GetAllOrganizationEntitlementInstances(Data.Organization.Id)

if instances.length > 0 then
    Log("Information", "Organization has " + instances.length + " entitlements")

    foreach instance in instances do
        Log("Information", "Entitlement: " + instance.entitlementDefinitionId)
        Log("Information", "  Start: " + instance.startDate)
        if instance.endDate != null then
            Log("Information", "  End: " + instance.endDate)
        else
            Log("Information", "  End: Indefinite")
        end
    end
else
    Log("Information", "Organization has no entitlements")
end
```

```jyro
# Check if organization has a specific entitlement
var premiumDef = GetEntitlementDefinitionByName("Premium Features")
var instances = GetAllOrganizationEntitlementInstances(Data.Organization.Id)
var hasPremium = false

foreach instance in instances do
    if instance.entitlementDefinitionId == premiumDef.id then
        hasPremium = true
        break
    end
end

if hasPremium then
    Log("Information", "Organization has Premium Features")
else
    Log("Information", "Organization does not have Premium Features")
end
```

```jyro
# Find expiring entitlements
var instances = GetAllOrganizationEntitlementInstances(Data.Organization.Id)
var today = Today()
var expiringInstances = []

foreach instance in instances do
    if instance.endDate != null then
        var daysUntilExpiry = DateDiff(today, instance.endDate, "days")
        if daysUntilExpiry >= 0 and daysUntilExpiry <= 30 then
            expiringInstances.add(instance)
        end
    end
end

if expiringInstances.length > 0 then
    Log("Warning", expiringInstances.length + " entitlements expiring within 30 days")

    foreach instance in expiringInstances do
        Log("Warning", "  Expiring: " + instance.id + " on " + instance.endDate)
    end
end
```

```jyro
# Count entitlements by type
var instances = GetAllOrganizationEntitlementInstances(Data.Organization.Id)
var allDefs = GetAllEntitlementDefinitions()

# Build definition lookup
var defMap = {}
foreach def in allDefs do
    defMap[def.id] = def.name
end

# Count by type
var countByType = {}
foreach instance in instances do
    var defName = defMap[instance.entitlementDefinitionId]
    if defName != null then
        if countByType[defName] == null then
            countByType[defName] = 0
        end
        countByType[defName] = countByType[defName] + 1
    end
end

Log("Information", "Entitlement summary:")
foreach key in Keys(countByType) do
    Log("Information", "  " + key + ": " + countByType[key])
end
```

```jyro
# Generate entitlement report for organization
var instances = GetAllOrganizationEntitlementInstances(Data.Organization.Id)
var allDefs = GetAllEntitlementDefinitions()

# Build lookup map
var defMap = {}
foreach def in allDefs do
    defMap[def.id] = def
end

var report = "Entitlement Report for " + Data.Organization.Name + "\n"
report = report + "Generated: " + Now() + "\n\n"

foreach instance in instances do
    var def = defMap[instance.entitlementDefinitionId]
    if def != null then
        report = report + "- " + def.name + "\n"
        report = report + "  Category: " + def.category + "\n"
        report = report + "  Started: " + instance.startDate + "\n"
        if instance.endDate != null then
            report = report + "  Expires: " + instance.endDate + "\n"
        else
            report = report + "  Expires: Never\n"
        end
        if instance.notes != null then
            report = report + "  Notes: " + instance.notes + "\n"
        end
        report = report + "\n"
    end
end

Log("Information", report)
```

```jyro
# Access facet values for entitlement instances
var instances = GetAllOrganizationEntitlementInstances(Data.Organization.Id)

foreach instance in instances do
    Log("Information", "Entitlement: " + instance.entitlementDefinitionId)

    # Check for facet instances
    if instance.facetInstances != null and instance.facetInstances.length > 0 then
        Log("Information", "  Facet values:")

        foreach facet in instance.facetInstances do
            # Use facetDefinitionName for display (no lookup needed!)
            Log("Information", "    " + facet.facetDefinitionName + ":")

            foreach value in facet.values do
                Log("Information", "      - " + value)
            end
        end
    end
end
```

## See Also

- [GetOrganizationEntitlementInstanceByName](GetOrganizationEntitlementInstanceByName.md) - Find single instance by name
- [GetAllEntitlementInstances](GetAllEntitlementInstances.md) - Get all instances across all organizations
- [AddEntitlementInstance](AddEntitlementInstance.md) - Grant new entitlement
- [RemoveEntitlementInstance](RemoveEntitlementInstance.md) - Revoke entitlement
- [GetAllEntitlementDefinitions](GetAllEntitlementDefinitions.md) - Get all definitions
