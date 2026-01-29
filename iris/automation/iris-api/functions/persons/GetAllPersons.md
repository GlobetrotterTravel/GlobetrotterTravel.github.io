---
layout: default
title: GetAllPersons
parent: Iris API functions
has_children: false
has_toc: false
---

# GetAllPersons

Retrieves all persons in the current tenant.

## Syntax

```jyro
GetAllPersons()
```

## Parameters

None

## Returns

- **array**: Array of Person objects, or empty array if error or no persons exist

## Description

Fetches all person records from the database for the current tenant. Returns an array of person objects containing all properties including id, firstName, lastName, emailAddress, organizationId, facetInstances, fieldInstances, and more.

This function is useful for bulk operations, reporting, or filtering persons based on specific criteria.

## Examples

```jyro
# Get all persons and iterate
var persons = GetAllPersons()
Log("Information", "Total persons: " + Length(persons))

foreach person in persons do
    Log("Information", person.firstName + " " + person.lastName)
end
```

```jyro
# Filter active persons
var persons = GetAllPersons()
var activePersons = []

foreach person in persons do
    if person.isActive == true then
        activePersons = activePersons + [person]
    end
end

Log("Information", "Active persons: " + Length(activePersons))
```

```jyro
# Find persons by email domain
var persons = GetAllPersons()

foreach person in persons do
    if EndsWith(person.emailAddress, "@vip.com") then
        Log("Information", "VIP person found: " + person.emailAddress)
    end
end
```

```jyro
# Get persons for a specific organization
var targetOrgId = Data.Organization.Id
var persons = GetAllPersons()

foreach person in persons do
    if person.organizationId == targetOrgId then
        Log("Information", "Org member: " + person.firstName + " " + person.lastName)
    end
end
```
