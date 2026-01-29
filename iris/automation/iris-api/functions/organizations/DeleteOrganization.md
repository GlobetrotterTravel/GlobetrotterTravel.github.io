---
layout: default
title: DeleteOrganization
parent: Iris API functions
has_children: false
has_toc: false
---

# DeleteOrganization

Deletes an organization by its ID. This is a hard delete from the database.

## Syntax

```jyro
DeleteOrganization(organizationId)
```

## Parameters

- **organizationId** (string): The GUID of the organization to delete

## Returns

- **boolean**: true if successful, false otherwise

## Description

Permanently removes an organization from the database. This operation cannot be undone. Consider using deactivation instead of deletion for data retention purposes.

## Examples

```jyro
# Delete organization by ID
var success = DeleteOrganization(Data.Organization.Id.Value)
if success then
    Log("Information", "Organization deleted successfully")
end
```

```jyro
# Validate before deleting
var org = GetOrganizationById(organizationId)
if org != null then
    var deleted = DeleteOrganization(organizationId)
    if not deleted then
        Log("Warning", "Failed to delete organization")
    end
end
```

```jyro
# Delete with error handling
var deleted = DeleteOrganization(orgId)
if not deleted then
    CancelAction("Failed to delete organization")
end
```

```jyro
# Delete all inactive organizations (cleanup script)
var allOrgs = GetAllOrganizations()
var deletedCount = 0

foreach org in allOrgs do
    if not org.isActive then
        var result = DeleteOrganization(org.id)
        if result then
            deletedCount = deletedCount + 1
            Log("Information", "Deleted inactive org: " + org.name)
        end
    end
end

Log("Information", "Cleanup complete. Deleted " + deletedCount + " organizations")
```
