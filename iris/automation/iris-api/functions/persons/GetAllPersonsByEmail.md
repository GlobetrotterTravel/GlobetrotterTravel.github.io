---
layout: default
title: GetAllPersonsByEmail
parent: Iris API functions
has_children: false
has_toc: false
---

# GetAllPersonsByEmail

Retrieves all persons with a matching email address.

## Syntax

```jyro
GetAllPersonsByEmail(emailAddress)
```

## Parameters

- **emailAddress** (string): The email address to search for (case-insensitive)

## Returns

- **array**: Array of Person objects with matching email, or empty array if none found

## Description

Searches for all persons in the current tenant with the specified email address. The search is case-insensitive. Unlike [GetPersonByEmail](GetPersonByEmail.md) which returns only the first match, this function returns ALL persons with that email address.

This function is particularly useful for:
- Finding duplicate person records that need to be merged
- Auditing data quality issues
- Bulk operations on persons with the same email

## Person Object Properties

Each person object in the returned array contains:

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
# Find all persons with email
var persons = GetAllPersonsByEmail("john@example.com")
Log("Information", "Found " + Length(persons) + " person(s) with this email")
```

```jyro
# Check for duplicates
var persons = GetAllPersonsByEmail(emailToCheck)
if Length(persons) > 1 then
    Log("Warning", "Found " + Length(persons) + " duplicate persons with email: " + emailToCheck)

    foreach person in persons do
        Log("Information", "  ID: " + person.id + " - " + person.firstName + " " + person.lastName)
        Log("Information", "    Organizations: " + Length(person.organizationIds))
    end
end
```

```jyro
# Dynamic endpoint - find duplicates for data cleanup
var email = Data.Request.query.email

if email is null or email == "" then
    Data._payload = {"error": "email parameter is required"}
    Data._statusCode = 400
    return
end

var persons = GetAllPersonsByEmail(email)

var duplicates = []
foreach person in persons do
    var entry = {
        "id": person.id,
        "firstName": person.firstName,
        "lastName": person.lastName,
        "isActive": person.isActive,
        "organizationCount": Length(person.organizationIds)
    }
    duplicates = duplicates + [entry]
end

Data._payload = {
    "email": email,
    "count": Length(persons),
    "isDuplicate": Length(persons) > 1,
    "persons": duplicates
}
Data._statusCode = 200
```

```jyro
# Merge duplicate persons - collect all organizations
var persons = GetAllPersonsByEmail("john@example.com")

if Length(persons) <= 1 then
    Log("Information", "No duplicates found")
    return
end

# Use first person as the primary
var primaryPerson = persons[0]
var primaryId = primaryPerson.id

Log("Information", "Primary person: " + primaryId)

# Collect all unique organizations from duplicates
var allOrgIds = []
foreach person in persons do
    foreach orgId in person.organizationIds do
        # Add to primary if not already there
        var success = AddPersonToOrganization(primaryId, orgId)
        if success then
            Log("Information", "Added org " + orgId + " to primary person")
        end
    end
end

# Deactivate duplicates (or delete them)
var i = 1
while i < Length(persons) do
    var duplicatePerson = persons[i]
    Log("Information", "Deactivating duplicate: " + duplicatePerson.id)
    # UpdatePerson or DeletePerson here
    i = i + 1
end

Log("Information", "Merge complete. Primary person now has all organizations.")
```

```jyro
# Report all duplicate emails in tenant
var allPersons = GetAllPersons()
var checkedEmails = []
var duplicateReport = []

foreach person in allPersons do
    var email = person.emailAddress

    # Skip if we already checked this email
    var alreadyChecked = false
    foreach checked in checkedEmails do
        if checked == email then
            alreadyChecked = true
        end
    end

    if alreadyChecked == false then
        checkedEmails = checkedEmails + [email]

        var matches = GetAllPersonsByEmail(email)
        if Length(matches) > 1 then
            var entry = {
                "email": email,
                "count": Length(matches)
            }
            duplicateReport = duplicateReport + [entry]
            Log("Warning", "Duplicate email: " + email + " (" + Length(matches) + " records)")
        end
    end
end

Log("Information", "Total duplicate emails found: " + Length(duplicateReport))
```

```jyro
# Handle empty result
var persons = GetAllPersonsByEmail("unknown@example.com")

if Length(persons) == 0 then
    Log("Information", "No persons found with that email")
end
```

## Related Functions

- [GetPersonByEmail](GetPersonByEmail.md) - Get first person with matching email
- [GetPersonById](GetPersonById.md) - Get person by ID
- [GetAllPersons](GetAllPersons.md) - Get all persons in tenant
- [AddPersonToOrganization](AddPersonToOrganization.md) - Add person to organization
- [RemovePersonFromOrganization](RemovePersonFromOrganization.md) - Remove person from organization
- [DeletePerson](DeletePerson.md) - Delete a person
