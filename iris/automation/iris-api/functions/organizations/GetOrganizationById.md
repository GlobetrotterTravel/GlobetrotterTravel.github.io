---
layout: default
title: GetOrganizationById
parent: Iris API functions
has_children: false
has_toc: false
---

# GetOrganizationById

Retrieves an organization by its unique identifier.

## Syntax

```jyro
GetOrganizationById(organizationId)
```

## Parameters

- **organizationId** (string): The GUID of the organization to retrieve

## Returns

- **object**: Organization object with all properties, or null if not found

## Description

Fetches a complete organization record from the database by its ID. Returns an object containing all organization properties including id, name, description, facetInstances, fieldInstances, and more.

## Examples

```jyro
# Get organization by ID
var org = GetOrganizationById("a1b2c3d4-5678-90ab-cdef-1234567890ab")
if org != null then
    Log("Information", "Found organization: " + org.name)
end
```

```jyro
# Access organization properties
var orgId = Data.Organization.Id.Value
var organization = GetOrganizationById(orgId)
if organization != null then
    Log("Information", "Organization: " + organization.name)
    Log("Information", "Active: " + organization.isActive)
    Log("Information", "Facet count: " + Length(organization.facetInstances))
end
```

```jyro
# Validate organization exists before operation
var targetOrgId = Data.Request.query.orgId
var targetOrg = GetOrganizationById(targetOrgId)
if targetOrg == null then
    Log("Error", "Organization not found")
    CancelAction("Invalid organization ID")
end
```
