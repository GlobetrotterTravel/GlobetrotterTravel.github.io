---
layout: default
title: GetCurrentUser
parent: Iris API functions
has_children: false
has_toc: false
---

# GetCurrentUser

Returns information about the currently authenticated user.

## Syntax

```jyro
GetCurrentUser()
```

## Parameters

None.

## Returns

An object with the following properties, or `null` if no HTTP context is available:

| Property | Type | Description |
|----------|------|-------------|
| `isAuthenticated` | boolean | Whether the user is authenticated |
| `name` | string | The user's display name (from Identity.Name) |
| `principalId` | string | The principal ID (e.g., "user:john@example.com") |
| `preferredUsername` | string | The user's preferred username (UPN) |
| `email` | string | The user's email address |
| `objectId` | string | Azure AD object ID (oid claim) |
| `tenantId` | string | Azure AD tenant ID (tid claim) |
| `roles` | array | Array of role names assigned to the user |
| `claims` | array | Array of all claims as `{type, value}` objects |

## Description

Retrieves user information from the current HTTP context's authenticated user (ClaimsPrincipal). This function extracts claims from the JWT token provided during authentication.

The function is particularly useful for:
- Personalizing content based on the current user
- Logging user activity
- Conditional logic based on user roles
- Building audit trails

## Examples

### Basic User Info

```jyro
var user = GetCurrentUser()

if user != null and user.isAuthenticated then
    Log("Information", "Request from: " + user.email)
    Log("Information", "Principal ID: " + user.principalId)
end
```

### Check User Roles

```jyro
var user = GetCurrentUser()

if user != null then
    var isAdmin = false
    foreach role in user.roles do
        if role == "admin" then
            isAdmin = true
            break
        end
    end

    if isAdmin then
        Log("Information", "Admin user detected: " + user.name)
    end
end
```

### Personalized Welcome Message

```jyro
var user = GetCurrentUser()

if user != null and user.isAuthenticated then
    Data.model = {
        "welcomeMessage": "Welcome, " + user.name + "!",
        "userEmail": user.email,
        "isAuthenticated": true
    }
else
    Data.model = {
        "welcomeMessage": "Welcome, Guest!",
        "userEmail": "",
        "isAuthenticated": false
    }
end

Data._payload = RenderTemplate("welcome", Data.model)
Data._contentType = "text/html"
Data._statusCode = 200
```

### Access All Claims

```jyro
var user = GetCurrentUser()

if user != null then
    Log("Information", "User has " + Length(user.claims) + " claims")

    foreach claim in user.claims do
        Log("Debug", "Claim: " + claim.type + " = " + claim.value)
    end
end
```

### Combining with GetCurrentTenant

```jyro
var user = GetCurrentUser()
var tenant = GetCurrentTenant()

Data.model = {
    "tenantName": tenant.name,
    "userName": user.name,
    "userEmail": user.email,
    "subdomain": tenant.subdomain
}

Data._payload = RenderTemplate("dashboard", Data.model)
Data._contentType = "text/html"
Data._statusCode = 200
```

## Notes

- Returns `null` if called from a context without an HTTP request (e.g., background jobs without user context)
- The `claims` array contains all claims from the JWT token, including custom claims
- The `principalId` follows the format `user:{email}` for users and `service:{name}` for service accounts
- Empty strings are returned for properties that don't have corresponding claims
