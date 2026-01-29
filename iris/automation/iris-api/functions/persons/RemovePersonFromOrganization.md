---
layout: default
title: RemovePersonFromOrganization
parent: Iris API functions
has_children: false
has_toc: false
---

# RemovePersonFromOrganization

Removes a person from an organization by removing the organization ID from the person's organization list.

## Syntax

```jyro
RemovePersonFromOrganization(personId, organizationId)
```

## Parameters

- **personId** (string): The GUID string of the person to update
- **organizationId** (string): The GUID string of the organization to remove

## Returns

- **boolean**: `true` if successful (or already not a member), `false` on error

## Description

Removes an organization from a person's `organizationIds` array. This function is idempotent - if the person is not a member of the specified organization, it returns `true` without making any changes.

**Important**: A person must belong to at least one organization. This function will return `false` if you attempt to remove the person's last organization.

## Behavior

| Scenario | Result |
|----------|--------|
| Person belongs to organization (and has others) | Removes org, returns `true` |
| Person does not belong to organization | Returns `true` (idempotent) |
| Person does not exist | Returns `false` |
| Organization is person's only organization | Returns `false` (prevented) |

## Examples

```jyro
# Remove person from organization
var success = RemovePersonFromOrganization(personId, orgId)
if success then
    Log("Information", "Person removed from organization successfully")
else
    Log("Error", "Failed to remove person from organization (may be last org)")
end
```

```jyro
# Idempotent - safe to call multiple times
RemovePersonFromOrganization(personId, orgId)  # First call removes
RemovePersonFromOrganization(personId, orgId)  # Second call returns true (not a member)
```

```jyro
# Check organization count before removing
var person = GetPersonById(personId)
if person is not null then
    var orgCount = Length(person.organizationIds)

    if orgCount > 1 then
        var success = RemovePersonFromOrganization(personId, orgId)
        Log("Information", "Remove result: " + success)
    else
        Log("Warning", "Cannot remove - person only has one organization")
    end
end
```

```jyro
# Move person from one organization to another
var personId = Data.Request.body.personId
var fromOrgId = Data.Request.body.fromOrganizationId
var toOrgId = Data.Request.body.toOrganizationId

# First add to new org (ensures they have at least one)
var addSuccess = AddPersonToOrganization(personId, toOrgId)

if addSuccess then
    # Then remove from old org
    var removeSuccess = RemovePersonFromOrganization(personId, fromOrgId)

    if removeSuccess then
        Data._payload = {"success": true, "message": "Person moved successfully"}
        Data._statusCode = 200
    else
        Data._payload = {"error": "Added to new org but failed to remove from old org"}
        Data._statusCode = 500
    end
else
    Data._payload = {"error": "Failed to add person to new organization"}
    Data._statusCode = 400
end
```

```jyro
# Clean up organization membership when org is being archived
BeforeOrganizationDeleted:
    var orgId = Data.Organization.Id
    var allPersons = GetAllPersons()

    foreach person in allPersons do
        # Check if person belongs to this org
        var belongsToOrg = false
        foreach pOrgId in person.organizationIds do
            if pOrgId == orgId then
                belongsToOrg = true
            end
        end

        if belongsToOrg then
            var success = RemovePersonFromOrganization(person.id, orgId)
            if success then
                Log("Information", "Removed " + person.emailAddress + " from organization")
            else
                Log("Warning", "Could not remove " + person.emailAddress + " (may be last org)")
            end
        end
    end
```

## Related Functions

- [AddPersonToOrganization](AddPersonToOrganization) - Add a person to an organization
- [GetPersonById](GetPersonById) - Get person details including organization membership
- [GetPersonByEmail](GetPersonByEmail) - Find a person by email address
- [UpdatePerson](UpdatePerson) - Update person properties
