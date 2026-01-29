---
layout: default
title: GetCurrentUserRoles
parent: Iris API functions
has_children: false
has_toc: false
---

# GetCurrentUserRoles

Retrieves the roles for the currently authenticated user.

## Syntax

```jyro
GetCurrentUserRoles()
```

## Parameters

None.

## Returns

- **array**: An array of role objects, or an empty array if the user is not authenticated or has no roles

## Description

A convenience function that retrieves the roles assigned to the currently authenticated user making the request. This is particularly useful in dynamic endpoints where you need to check the caller's permissions or role-based access.

The function:
1. Gets the current user from the HTTP context (via JWT claims)
2. Looks up the Person record by matching UserPrincipalName
3. Retrieves the roles from the associated Identity

Returns an empty array if:
- The user is not authenticated
- The user has no UPN claim
- No matching Person record exists
- No roles are assigned

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
# Get current user's roles in a dynamic endpoint
var roles = GetCurrentUserRoles()
if Length(roles) == 0 then
    Data._payload = {"error": "No roles assigned"}
    Data._statusCode = 403
    return
end
```

```jyro
# Check if current user has admin role
var roles = GetCurrentUserRoles()
var isAdmin = false
foreach role in roles do
    if role.name == "Admin" then
        isAdmin = true
    end
end
if not isAdmin then
    Data._payload = {"error": "Admin role required"}
    Data._statusCode = 403
    return
end
```

```jyro
# Get all permissions for current user
var roles = GetCurrentUserRoles()
var permissions = []
foreach role in roles do
    foreach perm in role.permissions do
        Append(permissions, perm)
    end
end
Log("Information", "Current user has permissions: " + Join(permissions, ", "))
```

```jyro
# Build user profile with roles
var user = GetCurrentUser()
var roles = GetCurrentUserRoles()

var roleNames = []
foreach role in roles do
    Append(roleNames, role.name)
end

Data._payload = {
    "user": {
        "name": user.name,
        "principalId": user.principalId,
        "isAuthenticated": user.isAuthenticated
    },
    "roles": roleNames,
    "isAdmin": Contains(roleNames, "Admin"),
    "hasTenantAccess": false
}

# Check for tenant-wide access
foreach role in roles do
    if role.scopeLevel == "Tenant" then
        Data._payload.hasTenantAccess = true
    end
end

Data._statusCode = 200
```

```jyro
# Restrict endpoint to specific roles
var roles = GetCurrentUserRoles()
var allowedRoles = ["Admin", "Manager", "Supervisor"]
var hasAccess = false

foreach role in roles do
    if Contains(allowedRoles, role.name) then
        hasAccess = true
    end
end

if not hasAccess then
    Data._payload = {"error": "Insufficient permissions"}
    Data._statusCode = 403
    return
end

# Continue with protected operation...
```

```jyro
# Check specific permission
var roles = GetCurrentUserRoles()
var canWriteOrgs = false

foreach role in roles do
    foreach perm in role.permissions do
        if perm == "organization:write" then
            canWriteOrgs = true
        end
    end
end

if not canWriteOrgs then
    Data._payload = {"error": "organization:write permission required"}
    Data._statusCode = 403
    return
end
```

## Related Functions

- [GetCurrentUser](../context/GetCurrentUser) - Get current user info (name, principalId)
- [GetPersonRoles](GetPersonRoles) - Get roles for any person by ID
- [AssignRoleToPerson](AssignRoleToPerson) - Assign a role to a person
- [RemoveRoleFromPerson](RemoveRoleFromPerson) - Remove a role from a person
