---
layout: default
title: GetCurrentTenant
parent: Iris API functions
has_children: false
has_toc: false
---

# GetCurrentTenant

Returns information about the current tenant.

## Syntax

```jyro
GetCurrentTenant()
```

## Parameters

None.

## Returns

An object with the following properties, or `null` if tenant not found:

| Property | Type | Description |
|----------|------|-------------|
| `id` | string | The tenant's unique identifier (GUID) |
| `name` | string | The tenant's display name |
| `subdomain` | string | The tenant's subdomain |
| `isActive` | boolean | Whether the tenant is currently active |
| `administratorEmailAddress` | string | The admin email address |
| `authorizedEntraTenantIds` | array | Array of authorized Azure AD tenant IDs |
| `authorizedEntraDomains` | array | Array of authorized email domains |
| `propertyValues` | object | Key-value pairs of tenant property values (keyed by PropertyDefinitionId) |
| `statistics` | object | Counts of various definitions |

### Statistics Object

| Property | Type | Description |
|----------|------|-------------|
| `organizationFacetDefinitionCount` | number | Number of organization facet definitions |
| `personFacetDefinitionCount` | number | Number of person facet definitions |
| `serviceAccountFacetDefinitionCount` | number | Number of service account facet definitions |
| `identityFacetDefinitionCount` | number | Number of identity facet definitions |
| `fieldDefinitionCount` | number | Number of field definitions |
| `hybridListDefinitionCount` | number | Number of hybrid list definitions |
| `setDefinitionCount` | number | Number of set definitions |
| `roleDefinitionCount` | number | Number of role definitions |
| `entitlementDefinitionCount` | number | Number of entitlement definitions |
| `secretCount` | number | Number of secrets |

## Description

Retrieves the current tenant's information from the database. The tenant is determined from:
1. The `TenantContextService` AsyncLocal value (for background jobs)
2. The HTTP context's `TenantId` item (set by `TenantContextMiddleware` from the subdomain)

This function is useful for:
- Building tenant-branded content
- Displaying tenant configuration
- Conditional logic based on tenant settings
- Dashboard statistics

## Examples

### Basic Tenant Info

```jyro
var tenant = GetCurrentTenant()

if tenant != null then
    Log("Information", "Current tenant: " + tenant.name)
    Log("Information", "Subdomain: " + tenant.subdomain)
    Log("Information", "Active: " + tenant.isActive)
end
```

### Build Dashboard with Statistics

```jyro
var tenant = GetCurrentTenant()

if tenant != null then
    Data.model = {
        "tenantName": tenant.name,
        "stats": {
            "facetDefinitions": tenant.statistics.organizationFacetDefinitionCount + tenant.statistics.personFacetDefinitionCount,
            "fieldDefinitions": tenant.statistics.fieldDefinitionCount,
            "hybridLists": tenant.statistics.hybridListDefinitionCount,
            "secrets": tenant.statistics.secretCount
        }
    }

    Data._payload = RenderTemplate("admin-dashboard", Data.model)
    Data._contentType = "text/html"
    Data._statusCode = 200
end
```

### Check Authorized Domains

```jyro
var tenant = GetCurrentTenant()
var user = GetCurrentUser()

if tenant != null and user != null then
    var userDomain = ""
    var emailParts = Split(user.email, "@")
    if Length(emailParts) > 1 then
        userDomain = emailParts[1]
    end

    var isAuthorizedDomain = false
    foreach domain in tenant.authorizedEntraDomains do
        if Lower(domain) == Lower(userDomain) then
            isAuthorizedDomain = true
            break
        end
    end

    if isAuthorizedDomain then
        Log("Information", "User domain is authorized: " + userDomain)
    else
        Log("Warning", "User domain not in authorized list: " + userDomain)
    end
end
```

### Dynamic Homepage

```jyro
var user = GetCurrentUser()
var tenant = GetCurrentTenant()
var orgs = GetAllOrganizations()
var persons = GetAllPersons()

Data.model = {
    "tenantId": tenant.id,
    "tenantName": tenant.name,
    "subdomain": tenant.subdomain,
    "administratorEmail": tenant.administratorEmailAddress,
    "userName": user.name,
    "userEmail": user.email,
    "isAuthenticated": user.isAuthenticated,
    "totalOrganizations": Length(orgs),
    "totalPersons": Length(persons),
    "statistics": tenant.statistics,
    "organizations": orgs,
    "persons": persons
}

Data._payload = RenderTemplate("home", Data.model)
Data._contentType = "text/html"
Data._statusCode = 200
```

### Tenant-Specific Branding

```jyro
var tenant = GetCurrentTenant()

var brandColor = "#0078d4"
var logoUrl = "/images/default-logo.png"

if tenant != null then
    # Check property values for custom branding
    # Note: propertyValues are keyed by PropertyDefinitionId (GUID)
    foreach key in tenant.propertyValues do
        var value = tenant.propertyValues[key]
        Log("Debug", "Property " + key + " = " + value)
    end
end

Data.model = {
    "tenantName": tenant.name,
    "brandColor": brandColor,
    "logoUrl": logoUrl
}

Data._payload = RenderTemplate("branded-header", Data.model)
Data._contentType = "text/html"
Data._statusCode = 200
```

## Notes

- Returns `null` if no tenant context is available or if the tenant doesn't exist in the database
- The `propertyValues` object is keyed by `PropertyDefinitionId` (GUID), not property name. Use `GetSecret()` for named key-value access.
- Statistics provide counts without loading the full definition collections
- The tenant is loaded fresh from the database on each call (not cached within the script)
