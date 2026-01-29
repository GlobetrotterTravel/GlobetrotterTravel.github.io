---
layout: default
title: GetPersonRoles
parent: Iris API functions
has_children: false
has_toc: false
---

# GetPersonRoles

Retrieves the roles assigned to a person.

## Syntax

```jyro
GetPersonRoles(personId)
```

## Parameters

- **personId** (string): The GUID string of the person whose roles to retrieve

## Returns

- **array**: An array of role objects, or an empty array if no roles are assigned or the person is not found

## Description

Fetches all role definitions assigned to a person. Roles are stored on the Identity record associated with the person via their UserPrincipalName (UPN). If the person does not have a UPN or no Identity is found, an empty array is returned.

## Role Object Properties

Each role object in the returned array contains:

- **id** (string): The role definition's unique identifier (GUID)
- **name** (string): The role name (e.g., "Admin", "User", "ReadOnly")
- **description** (string|null): A description of the role
- **scopeLevel** (string): The scope level - "Tenant", "Hierarchy", "Organization", or "Self"
- **isActive** (boolean): Whether the role is currently active
- **permissions** (array): Array of permission strings in format "resource:operation"

## Examples

```jyro
# Get roles for a person
var roles = GetPersonRoles(personId)
if Length(roles) > 0 then
    Log("Information", "Person has " + Length(roles) + " role(s)")
    foreach role in roles do
        Log("Information", "  - " + role.name + " (" + role.scopeLevel + ")")
    end
end
```

```jyro
# Check if person has a specific role
var roles = GetPersonRoles(personId)
var isAdmin = false
foreach role in roles do
    if role.name == "Admin" then
        isAdmin = true
    end
end
if isAdmin then
    Log("Information", "Person is an admin")
end
```

```jyro
# Get all permissions for a person
var roles = GetPersonRoles(personId)
var permissions = []
foreach role in roles do
    foreach perm in role.permissions do
        Append(permissions, perm)
    end
end
Log("Information", "Person has " + Length(permissions) + " permissions")
```

```jyro
# Check role scope in dynamic endpoint
var personId = Data.Request.query.personId
var roles = GetPersonRoles(personId)

var hasTenantAccess = false
foreach role in roles do
    if role.scopeLevel == "Tenant" then
        hasTenantAccess = true
    end
end

Data._payload = {
    "personId": personId,
    "roleCount": Length(roles),
    "hasTenantAccess": hasTenantAccess
}
Data._statusCode = 200
```

```jyro
# Build role summary for person
var person = GetPersonById(personId)
var roles = GetPersonRoles(personId)

if person is not null then
    var roleNames = []
    foreach role in roles do
        Append(roleNames, role.name)
    end

    Data._payload = {
        "person": {
            "id": person.id,
            "name": person.firstName + " " + person.lastName,
            "email": person.emailAddress
        },
        "roles": roleNames,
        "isAdmin": Contains(roleNames, "Admin")
    }
    Data._statusCode = 200
end
```

## Related Functions

- [AssignRoleToPerson](AssignRoleToPerson.md) - Assign a role to a person
- [RemoveRoleFromPerson](RemoveRoleFromPerson.md) - Remove a role from a person
- [GetCurrentUserRoles](GetCurrentUserRoles.md) - Get roles for the current authenticated user
- [GetPersonById](../persons/GetPersonById.md) - Get person details
