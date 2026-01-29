---
layout: default
title: AssignRoleToPerson
parent: Iris API functions
has_children: false
has_toc: false
---

# AssignRoleToPerson

Assigns a role to a person.

## Syntax

```jyro
AssignRoleToPerson(personId, roleId)
```

## Parameters

- **personId** (string): The GUID string of the person to assign the role to
- **roleId** (string): The GUID string of the role definition to assign

## Returns

- **boolean**: true if the role was successfully assigned, false otherwise

## Description

Assigns a role definition to a person. The role is stored on the Identity record associated with the person via their UserPrincipalName (UPN).

**Important**: The person must have a valid UserPrincipalName to be assigned roles. If the person does not have a UPN, or if no matching Identity exists, the assignment will fail.

## Examples

```jyro
# Assign a role to a person
var success = AssignRoleToPerson(personId, roleId)
if success then
    Log("Information", "Role assigned successfully")
end
```

```jyro
# Look up role by name first, then assign
var roles = GetAllRoleDefinitions()
var adminRole = null
foreach role in roles do
    if role.name == "Admin" then
        adminRole = role
    end
end
if adminRole is not null then
    AssignRoleToPerson(personId, adminRole.id)
end
```

```jyro
# Assign role with validation
var person = GetPersonById(personId)
if person is not null and person.userPrincipalName is not null then
    var assigned = AssignRoleToPerson(personId, roleId)
    if not assigned then
        Log("Warning", "Failed to assign role to " + person.emailAddress)
    end
end
```

```jyro
# Assign default role when person is created
# (in AfterPersonCreated event hook)
var roles = GetAllRoleDefinitions()
var defaultRole = null
foreach role in roles do
    if role.name == "User" then
        defaultRole = role
    end
end

if defaultRole is not null then
    var person = Data.Person
    if person.userPrincipalName is not null then
        var assigned = AssignRoleToPerson(person.id, defaultRole.id)
        if assigned then
            Log("Information", "Assigned default User role to " + person.emailAddress)
        end
    end
end
```

```jyro
# Dynamic endpoint to assign role
var personId = Data.Request.body.personId
var roleId = Data.Request.body.roleId

# Validate person exists and has UPN
var person = GetPersonById(personId)
if person is null then
    Data._payload = {"error": "Person not found"}
    Data._statusCode = 404
    return
end

if person.userPrincipalName is null then
    Data._payload = {"error": "Person must have a UPN to be assigned roles"}
    Data._statusCode = 400
    return
end

var success = AssignRoleToPerson(personId, roleId)
if success then
    Data._payload = {"success": true, "message": "Role assigned"}
    Data._statusCode = 200
else
    Data._payload = {"error": "Failed to assign role"}
    Data._statusCode = 500
end
```

```jyro
# Promote user to admin
var person = GetPersonById(personId)
var roles = GetAllRoleDefinitions()

var adminRole = null
foreach role in roles do
    if role.name == "Admin" then
        adminRole = role
    end
end

if person is not null and adminRole is not null then
    var assigned = AssignRoleToPerson(person.id, adminRole.id)
    if assigned then
        SendNotification(
            person.emailAddress,
            "Role Upgraded",
            "You have been granted Admin privileges.",
            "Success",
            "/admin"
        )
    end
end
```

## Related Functions

- [RemoveRoleFromPerson](RemoveRoleFromPerson) - Remove a role from a person
- [GetPersonRoles](GetPersonRoles) - Get roles assigned to a person
- [GetCurrentUserRoles](GetCurrentUserRoles) - Get roles for the current authenticated user
- [GetPersonById](../persons/GetPersonById) - Get person details
