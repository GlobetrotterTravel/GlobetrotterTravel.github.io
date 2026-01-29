---
layout: default
title: SetOrganizationParent
parent: Iris API functions
has_children: false
has_toc: false
---

# SetOrganizationParent

Sets or changes the parent organization for an existing organization.

## Syntax

```jyro
SetOrganizationParent(organizationId, parentOrganizationId, updatedBy)
```

## Parameters

- **organizationId** (string): The GUID of the organization to update
- **parentOrganizationId** (string|null): The GUID of the new parent organization, or null to remove parent
- **updatedBy** (string, optional): Identifier of who made the change (defaults to "System")

## Returns

- **boolean**: true if successful, false if failed

## Description

Updates the parent-child relationship for an organization in the hierarchy. This allows you to move organizations within the organizational tree structure. Pass null for the parentOrganizationId to make an organization a root-level organization.

## Examples

```jyro
# Set a parent for an organization
var success = SetOrganizationParent(childOrgId, parentOrgId, "System")

if success then
    Log("Information", "Parent set successfully")
else
    Log("Error", "Failed to set parent")
end
```

```jyro
# Move organization to different parent
var orgId = Data.Organization.Id
var newParentId = Data.Request.query.newParentId

var success = SetOrganizationParent(orgId, newParentId, GetCurrentUser().principalId)

if success then
    Log("Information", "Organization moved to new parent")
    BroadcastNotification(
        "Organization Moved",
        "Organization hierarchy has been updated",
        "Info",
        "/organizations/" + orgId,
        true
    )
end
```

```jyro
# Remove parent (make root organization)
var success = SetOrganizationParent(orgId, null, "System")

if success then
    Log("Information", "Organization is now a root organization")
end
```

```jyro
# Restructure organization hierarchy from CSV
var rows = ReadCsv(Data.ExternalFile)

foreach row in rows do
    var org = GetOrganizationById(row.OrgId)
    if org != null then
        var parentId = row.NewParentId
        if parentId == "" then
            parentId = null
        end

        var success = SetOrganizationParent(row.OrgId, parentId, "Restructure Script")

        if success then
            Log("Information", "Updated parent for: " + org.name)
        else
            Log("Error", "Failed to update: " + org.name)
        end
    end
end
```
