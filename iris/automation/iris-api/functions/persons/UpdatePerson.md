---
layout: default
title: UpdatePerson
parent: Iris API functions
has_children: false
has_toc: false
---

# UpdatePerson

Updates an existing person's information.

## Syntax

```jyro
UpdatePerson(personId, updateData)
```

## Parameters

- **personId** (string): The GUID string of the person to update
- **updateData** (object): An object containing the properties to update:
  - **firstName** (string|null): Person's first name (optional)
  - **lastName** (string): Person's last name (required)
  - **emailAddress** (string): Person's email address (required)
  - **organizationId** (string): GUID of the primary organization (required)
  - **userPrincipalName** (string|null): Azure AD UPN for SSO login (optional)
  - **isActive** (boolean): Whether the person is active (optional, defaults to true)

## Returns

- **boolean**: true if the update was successful, false otherwise

## Description

Updates an existing person record in the database. All required fields must be provided in the updateData object, even if they are not changing. This ensures data integrity and prevents accidental null values.

## Examples

```jyro
# Update a person's name
var success = UpdatePerson(personId, {
    firstName: "John",
    lastName: "Smith",
    emailAddress: "john.smith@example.com",
    organizationId: "12345678-1234-1234-1234-123456789abc"
})

if success then
    Log("Information", "Person updated successfully")
else
    Log("Error", "Failed to update person")
end
```

```jyro
# Deactivate a person
var person = GetPersonById(personId)
if person is not null then
    var success = UpdatePerson(personId, {
        firstName: person.firstName,
        lastName: person.lastName,
        emailAddress: person.emailAddress,
        organizationId: person.organizationIds[0],
        isActive: false
    })

    if success then
        Log("Information", "Person deactivated: " + person.emailAddress)
    end
end
```

```jyro
# Update person's UPN for SSO access
var person = GetPersonById(personId)
if person is not null then
    var success = UpdatePerson(personId, {
        firstName: person.firstName,
        lastName: person.lastName,
        emailAddress: person.emailAddress,
        organizationId: person.organizationIds[0],
        userPrincipalName: "john.smith@company.onmicrosoft.com",
        isActive: person.isActive
    })

    if success then
        Log("Information", "UPN updated for " + person.emailAddress)
    end
end
```

```jyro
# Update in a dynamic endpoint
var personId = Data.Request.query.personId
var body = Data.Request.body

var person = GetPersonById(personId)
if person is null then
    Data._payload = {"error": "Person not found"}
    Data._statusCode = 404
    return
end

var success = UpdatePerson(personId, {
    firstName: body.firstName,
    lastName: body.lastName,
    emailAddress: body.emailAddress,
    organizationId: person.organizationIds[0],
    isActive: true
})

if success then
    Data._payload = {"success": true, "message": "Person updated"}
    Data._statusCode = 200
else
    Data._payload = {"error": "Update failed"}
    Data._statusCode = 500
end
```

## Related Functions

- [GetPersonById](GetPersonById) - Retrieve a person by ID
- [CreatePerson](CreatePerson) - Create a new person
- [DeletePerson](DeletePerson) - Delete a person
