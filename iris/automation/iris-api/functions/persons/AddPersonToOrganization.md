---
layout: default
title: AddPersonToOrganization
parent: Iris API functions
has_children: false
has_toc: false
---

# AddPersonToOrganization

Adds a person to an organization by adding the organization ID to the person's organization list.

## Syntax

```jyro
AddPersonToOrganization(personId, organizationId)
```

## Parameters

- **personId** (string): The GUID string of the person to update
- **organizationId** (string): The GUID string of the organization to add

## Returns

- **boolean**: `true` if successful (or already a member), `false` on error

## Description

Adds an organization to a person's `organizationIds` array, allowing the person to be associated with multiple organizations. This function is idempotent - if the person is already a member of the specified organization, it returns `true` without making any changes.

The function performs the following validations:
- Verifies the organization exists before adding
- Verifies the person exists
- Checks if the person is already a member (returns `true` if so)
- Ensures the person doesn't exceed the maximum of 50 organizations

## Behavior

| Scenario | Result |
|----------|--------|
| Person and organization exist, not yet a member | Adds org, returns `true` |
| Person already belongs to organization | Returns `true` (idempotent) |
| Person does not exist | Returns `false` |
| Organization does not exist | Returns `false` |
| Person already has 50 organizations | Returns `false` |

## Examples

```jyro
# Add person to organization
var success = AddPersonToOrganization(personId, orgId)
if success then
    Log("Information", "Person added to organization successfully")
else
    Log("Error", "Failed to add person to organization")
end
```

```jyro
# Idempotent - safe to call multiple times
AddPersonToOrganization(personId, orgId)  # First call adds
AddPersonToOrganization(personId, orgId)  # Second call returns true (already member)
```

```jyro
# Add person to multiple organizations
var orgs = ["org-id-1", "org-id-2", "org-id-3"]
foreach orgId in orgs do
    var success = AddPersonToOrganization(personId, orgId)
    if success then
        Log("Information", "Added to org: " + orgId)
    else
        Log("Warning", "Failed to add to org: " + orgId)
    end
end
```

```jyro
# In AfterPersonCreated hook - add to additional organizations
AfterPersonCreated:
    var additionalOrgs = GetOrganizationsBySet("Premium Partners")

    foreach org in additionalOrgs do
        AddPersonToOrganization(Data.Person.Id, org.id)
    end

    Log("Information", "Added person to " + Length(additionalOrgs) + " partner organizations")
```

```jyro
# Dynamic endpoint - assign person to organization
var personId = Data.Request.body.personId
var orgId = Data.Request.body.organizationId

if personId is null or orgId is null then
    Data._payload = {"error": "personId and organizationId are required"}
    Data._statusCode = 400
    return
end

var success = AddPersonToOrganization(personId, orgId)

if success then
    Data._payload = {"success": true, "message": "Person added to organization"}
    Data._statusCode = 200
else
    Data._payload = {"error": "Failed to add person to organization"}
    Data._statusCode = 400
end
```

## Related Functions

- [RemovePersonFromOrganization](RemovePersonFromOrganization.md) - Remove a person from an organization
- [GetPersonById](GetPersonById.md) - Get person details including organization membership
- [GetPersonByEmail](GetPersonByEmail.md) - Find a person by email address
- [GetAllPersonsByEmail](GetAllPersonsByEmail.md) - Find all persons with matching email
- [UpdatePerson](UpdatePerson.md) - Update person properties
