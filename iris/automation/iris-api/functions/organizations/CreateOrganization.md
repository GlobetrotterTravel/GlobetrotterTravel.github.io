---
layout: default
title: CreateOrganization
parent: Iris API functions
has_children: false
has_toc: false
---

# CreateOrganization

Creates a new organization in the current tenant.

## Syntax

```jyro
CreateOrganization(number, name, hybridListItemPolicy, isActive, inheritParentFieldInstances, inheritParentEntitlements, inheritParentFacets, inheritParentHybridLists, description, parentOrganizationId, createdBy)
```

## Parameters

- **number** (string): Unique organization number/code (e.g., "C01234")
- **name** (string): Organization name
- **hybridListItemPolicy** (string): "Replace" or "Extend" - how hybrid list items are inherited
- **isActive** (boolean): Whether the organization is active
- **inheritParentFieldInstances** (boolean): Inherit field values from parent
- **inheritParentEntitlements** (boolean): Inherit entitlements from parent
- **inheritParentFacets** (boolean): Inherit facet values from parent
- **inheritParentHybridLists** (boolean): Inherit hybrid list selections from parent
- **description** (string, optional): Organization description
- **parentOrganizationId** (string|null, optional): Parent organization GUID for hierarchy
- **createdBy** (string, optional): Identifier of creator (defaults to "System")

## Returns

- **object**: The created Organization object, or null if creation failed

## Description

Creates a new organization record with the specified properties. Organizations can be arranged in a hierarchy by specifying a parent organization. Child organizations can inherit various properties from their parents based on the inheritance flags.

## Examples

```jyro
# Create a root organization
var org = CreateOrganization(
    "C01234",
    "Acme Corporation",
    "Replace",
    true,
    false,
    false,
    false,
    false,
    "Main corporate entity",
    null,
    "System"
)

if org != null then
    Log("Information", "Created organization: " + org.name + " with ID: " + org.id)
end
```

```jyro
# Create a child organization with inheritance
var parentOrg = GetOrganizationById(parentOrgId)

var childOrg = CreateOrganization(
    "C04567",
    "Acme Sales Division",
    "Extend",
    true,
    true,   # Inherit fields
    true,   # Inherit entitlements
    true,   # Inherit facets
    false,  # Don't inherit hybrid lists
    "Sales department",
    parentOrg.id,
    GetCurrentUser().principalId
)

if childOrg != null then
    Log("Information", "Created child org under " + parentOrg.name)
end
```

```jyro
# Bulk create organizations from CSV
var rows = ReadCsv(Data.ExternalFile)

foreach row in rows do
    var org = CreateOrganization(
        row.OrgNumber,
        row.OrgName,
        "Replace",
        true,
        false,
        false,
        false,
        false,
        row.Description,
        null,
        "CSV Import"
    )

    if org != null then
        Log("Information", "Created: " + org.name)

        # Set a facet on the new organization
        var industryFacetId = GetFacetDefinitionByName("Industry")
        SetOrganizationFacetInstance(org.id, industryFacetId, [row.Industry])
    else
        Log("Error", "Failed to create: " + row.OrgName)
    end
end
```

```jyro
# Create organization hierarchy
var rootOrg = CreateOrganization("HQ001", "Headquarters", "Replace", true, false, false, false, false, "Main HQ", null, "System")

if rootOrg != null then
    var regions = ["North", "South", "East", "West"]
    var counter = 1

    foreach region in regions do
        var regionOrg = CreateOrganization(
            "REG00" + counter,
            region + " Region",
            "Extend",
            true,
            true,
            true,
            true,
            true,
            region + " regional office",
            rootOrg.id,
            "System"
        )
        counter = counter + 1
    end

    Log("Information", "Created HQ with 4 regional offices")
end
```
