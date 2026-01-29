---
layout: default
title: RemoveRoleFromPerson
parent: Iris API functions
has_children: false
has_toc: false
---

# RemoveRoleFromPerson

Removes a role from a person.

## Syntax

```jyro
RemoveRoleFromPerson(personId, roleId)
```

## Parameters

- **personId** (string): The GUID string of the person to remove the role from
- **roleId** (string): The GUID string of the role definition to remove

## Returns

- **boolean**: true if the role was successfully removed, false otherwise

## Description

Removes a role definition from a person. The role is removed from the Identity record associated with the person via their UserPrincipalName (UPN). Returns false if the person or role is not found, or if the removal fails for any reason.

## Examples

```jyro
# Remove a role from a person
var success = RemoveRoleFromPerson(personId, roleId)
if success then
    Log("Information", "Role removed successfully")
end
```

```jyro
# Remove all roles from a person
var roles = GetPersonRoles(personId)
foreach role in roles do
    RemoveRoleFromPerson(personId, role.id)
end
Log("Information", "All roles removed from person")
```

```jyro
# Remove specific role by name
var roles = GetPersonRoles(personId)
foreach role in roles do
    if role.name == "Admin" then
        var removed = RemoveRoleFromPerson(personId, role.id)
        if removed then
            Log("Information", "Admin role removed")
        end
    end
end
```

```jyro
# Demote admin to regular user
var person = GetPersonById(personId)
var roles = GetPersonRoles(personId)

foreach role in roles do
    if role.name == "Admin" then
        var removed = RemoveRoleFromPerson(personId, role.id)
        if removed then
            SendNotification(
                person.emailAddress,
                "Role Changed",
                "Your Admin privileges have been revoked.",
                "Warning",
                null
            )
            Log("Information", "Removed Admin role from " + person.emailAddress)
        end
    end
end
```

```jyro
# Dynamic endpoint to remove role
var personId = Data.Request.body.personId
var roleId = Data.Request.body.roleId

var success = RemoveRoleFromPerson(personId, roleId)
if success then
    Data._payload = {"success": true, "message": "Role removed"}
    Data._statusCode = 200
else
    Data._payload = {"error": "Failed to remove role"}
    Data._statusCode = 500
end
```

```jyro
# Remove expired temporary roles
# (in a scheduled event hook)
var persons = GetAllPersons()

foreach person in persons do
    var roles = GetPersonRoles(person.id)
    foreach role in roles do
        if role.name == "TemporaryAccess" then
            # Check if role should be expired (custom logic)
            var removed = RemoveRoleFromPerson(person.id, role.id)
            if removed then
                Log("Information", "Removed expired role from " + person.emailAddress)
            end
        end
    end
end
```

## Related Functions

- [AssignRoleToPerson](AssignRoleToPerson.md) - Assign a role to a person
- [GetPersonRoles](GetPersonRoles.md) - Get roles assigned to a person
- [GetCurrentUserRoles](GetCurrentUserRoles.md) - Get roles for the current authenticated user
- [GetPersonById](../persons/GetPersonById.md) - Get person details
