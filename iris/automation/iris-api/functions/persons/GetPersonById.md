---
layout: default
title: GetPersonById
parent: Iris API functions
has_children: false
has_toc: false
---

# GetPersonById

Retrieves a person by their unique identifier.

## Syntax

```jyro
GetPersonById(personId)
```

## Parameters

- **personId** (string): The GUID string of the person to retrieve

## Returns

- **object**: The Person object if found, or null if not found

## Description

Fetches a person record from the database by its unique identifier. Returns the complete person object including all properties such as name, email, organization associations, and active status. Returns null if the person does not exist or if an error occurs.

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
- **fieldInstances** (array): Field instances assigned to the person

## Examples

```jyro
# Get person by ID
var person = GetPersonById("12345678-1234-1234-1234-123456789abc")
if person is not null then
    Log("Information", "Found person: " + person.firstName + " " + person.lastName)
end
```

```jyro
# Access person properties
var person = GetPersonById(personId)
if person is not null then
    Log("Information", "Email: " + person.emailAddress)
    Log("Information", "UPN: " + person.userPrincipalName)
    Log("Information", "Active: " + person.isActive)
end
```

```jyro
# Use in event hook to validate person exists
var person = GetPersonById(Data.Request.query.personId)
if person is null then
    Data._payload = {"error": "Person not found"}
    Data._statusCode = 404
    return
end

Data._payload = {
    "id": person.id,
    "name": person.firstName + " " + person.lastName,
    "email": person.emailAddress
}
Data._statusCode = 200
```

```jyro
# Check person's organization membership
var person = GetPersonById(personId)
if person is not null then
    var orgCount = Length(person.organizationIds)
    Log("Information", "Person belongs to " + orgCount + " organization(s)")

    foreach orgId in person.organizationIds do
        var org = GetOrganizationById(orgId)
        if org is not null then
            Log("Information", "  - " + org.name)
        end
    end
end
```

## Related Functions

- [GetAllPersons](GetAllPersons) - Get all persons in the tenant
- [CreatePerson](CreatePerson) - Create a new person
- [UpdatePerson](UpdatePerson) - Update an existing person
- [DeletePerson](DeletePerson) - Delete a person
- [GetPersonRoles](../roles/GetPersonRoles) - Get roles assigned to a person
