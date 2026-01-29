---
layout: default
title: CreatePerson
parent: Iris API functions
has_children: false
has_toc: false
---

# CreatePerson

Creates a new person in the specified organization.

## Syntax

```jyro
CreatePerson(organizationId, lastName, emailAddress, firstName, userPrincipalName, isActive, createdBy)
```

## Parameters

- **organizationId** (string): The GUID of the organization the person belongs to
- **lastName** (string): Person's last name (required)
- **emailAddress** (string): Person's email address (required)
- **firstName** (string|null): Person's first name (can be null)
- **userPrincipalName** (string|null): Azure AD UPN for SSO login (can be null)
- **isActive** (boolean): Whether the person is active
- **createdBy** (string, optional): Identifier of who created the person (defaults to "System")

## Returns

- **object**: The created Person object, or null if creation failed

## Description

Creates a new person record in the database. The person is associated with the specified organization. If a userPrincipalName is provided, the person can authenticate via Azure AD SSO.

## Examples

```jyro
# Create a person with minimal info
var person = CreatePerson(
    orgId,
    "Doe",
    "john.doe@example.com",
    "John",
    null,
    true,
    "System"
)

if person != null then
    Log("Information", "Created person: " + person.id)
end
```

```jyro
# Create a person with UPN for SSO
var person = CreatePerson(
    Data.Organization.Id,
    "Smith",
    "jane.smith@example.com",
    "Jane",
    "jane.smith@company.onmicrosoft.com",
    true,
    GetCurrentUser().principalId
)

if person != null then
    SendNotification(
        person.emailAddress,
        "Welcome!",
        "Your account has been created.",
        "Success",
        "/profile"
    )
end
```

```jyro
# Create inactive person for later activation
var pendingPerson = CreatePerson(
    orgId,
    "Johnson",
    "pending@example.com",
    "Pending",
    null,
    false,
    "Import Script"
)

Log("Information", "Created pending person: " + pendingPerson.emailAddress)
```

```jyro
# Bulk import from CSV data
var rows = ReadCsv(Data.ExternalFile)

foreach row in rows do
    var person = CreatePerson(
        Data.Organization.Id,
        row.LastName,
        row.Email,
        row.FirstName,
        null,
        true,
        "CSV Import"
    )

    if person != null then
        Log("Information", "Imported: " + person.emailAddress)
    else
        Log("Error", "Failed to import: " + row.Email)
    end
end
```
