---
layout: default
title: GetPersonByEmail
parent: Iris API functions
has_children: false
has_toc: false
---

# GetPersonByEmail

Retrieves a person by their email address.

## Syntax

```jyro
GetPersonByEmail(emailAddress)
```

## Parameters

- **emailAddress** (string): The email address to search for (case-insensitive)

## Returns

- **object**: The Person object if found, or `null` if not found

## Description

Searches for a person in the current tenant by their email address. The search is case-insensitive, so "JOHN@EXAMPLE.COM" will match "john@example.com".

If multiple persons exist with the same email address (duplicates), this function returns only the first match. To find all persons with a given email, use [GetAllPersonsByEmail](GetAllPersonsByEmail) instead.

## Person Object Properties

The returned person object contains:

- **id** (string): The person's unique identifier (GUID)
- **firstName** (string|null): Person's first name
- **lastName** (string): Person's last name
- **emailAddress** (string): Person's email address
- **userPrincipalName** (string|null): Azure AD UPN for SSO login
- **organizationIds** (array): Array of organization GUIDs the person belongs to
- **isActive** (boolean): Whether the person is active
- **facetInstances** (array): Facet instances assigned to the person

## Examples

```jyro
# Get person by email
var person = GetPersonByEmail("john@example.com")
if person is not null then
    Log("Information", "Found person: " + person.firstName + " " + person.lastName)
    Log("Information", "Person ID: " + person.id)
else
    Log("Warning", "Person not found with that email")
end
```

```jyro
# Case-insensitive matching
var person = GetPersonByEmail("JOHN@EXAMPLE.COM")  # Finds john@example.com
if person is not null then
    Log("Information", "Found: " + person.emailAddress)
end
```

```jyro
# Use in BeforePersonCreated to check for duplicates
BeforePersonCreated:
    var existingPerson = GetPersonByEmail(Data.Person.EmailAddress)

    if existingPerson is not null then
        Log("Warning", "A person with this email already exists: " + existingPerson.id)
        CancelAction("Duplicate email address detected")
    end
```

```jyro
# Dynamic endpoint - lookup person by email
var email = Data.Request.query.email

if email is null or email == "" then
    Data._payload = {"error": "email parameter is required"}
    Data._statusCode = 400
    return
end

var person = GetPersonByEmail(email)

if person is null then
    Data._payload = {"error": "Person not found"}
    Data._statusCode = 404
    return
end

Data._payload = {
    "id": person.id,
    "name": person.firstName + " " + person.lastName,
    "email": person.emailAddress,
    "organizationCount": Length(person.organizationIds)
}
Data._statusCode = 200
```

```jyro
# Look up person and add to organization
var email = Data.Request.body.email
var orgId = Data.Request.body.organizationId

var person = GetPersonByEmail(email)

if person is null then
    Data._payload = {"error": "Person not found with email: " + email}
    Data._statusCode = 404
    return
end

var success = AddPersonToOrganization(person.id, orgId)

if success then
    Data._payload = {"success": true, "personId": person.id}
    Data._statusCode = 200
else
    Data._payload = {"error": "Failed to add person to organization"}
    Data._statusCode = 500
end
```

```jyro
# Validate email and get person details
var person = GetPersonByEmail(Data.Request.body.email)

if person is null then
    Data._payload = {"valid": false, "reason": "Email not registered"}
    Data._statusCode = 200
    return
end

if person.isActive == false then
    Data._payload = {"valid": false, "reason": "Account is inactive"}
    Data._statusCode = 200
    return
end

Data._payload = {
    "valid": true,
    "personId": person.id,
    "displayName": person.firstName + " " + person.lastName
}
Data._statusCode = 200
```

## Related Functions

- [GetAllPersonsByEmail](GetAllPersonsByEmail) - Get all persons with matching email (find duplicates)
- [GetPersonById](GetPersonById) - Get person by ID
- [GetAllPersons](GetAllPersons) - Get all persons in tenant
- [AddPersonToOrganization](AddPersonToOrganization) - Add person to organization
- [CreatePerson](CreatePerson) - Create a new person
