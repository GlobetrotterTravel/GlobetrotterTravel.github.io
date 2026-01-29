---
layout: default
title: GetPersonFacetInstance
parent: Iris API functions
has_children: false
has_toc: false
---

# GetPersonFacetInstance

Retrieves a facet instance from a person.

## Syntax

```jyro
GetPersonFacetInstance(personId, facetDefinitionId)
```

## Parameters

- **personId** (string): The GUID of the person
- **facetDefinitionId** (string): The GUID of the facet definition

## Returns

- **object**: Facet instance object with the following structure, or null if not found:
  - `id` (string): The facet instance ID
  - `facetDefinitionId` (string): The facet definition ID
  - `values` (array): Array of string values

## Description

Retrieves a specific facet instance from a person by facet definition ID. Returns null if the person doesn't have an instance of the specified facet definition.

## Examples

```jyro
# Get a person's skills facet
var skillsFacet = GetFacetDefinitionByName("Skills")
var skills = GetPersonFacetInstance(Data.Person.Id.Value, skillsFacet.id)

if skills != null then
    Log("Information", "Person has " + skills.values.length + " skills")
    foreach skill in skills.values do
        Log("Debug", "Skill: " + skill)
    end
else
    Log("Information", "Person has no skills facet")
end
```

```jyro
# Check if person has a specific certification
var certFacet = GetFacetDefinitionByName("Certifications")
var certs = GetPersonFacetInstance(personId, certFacet.id)

if certs != null and certs.values.contains("AWS Certified") then
    Log("Information", "Person is AWS certified")
end
```

```jyro
# Use in AfterPersonUpdated hook to check changes
var departmentFacet = GetFacetDefinitionByName("Department")
var dept = GetPersonFacetInstance(Data.Current.Id.Value, departmentFacet.id)

if dept != null and dept.values[0] == "Engineering" then
    # Add to engineering mailing list
    SendNotification(
        "hr@example.com",
        "New Engineering Team Member",
        Data.Current.FirstName + " " + Data.Current.LastName + " is now in Engineering",
        "Info",
        "/persons/" + Data.Current.Id.Value
    )
end
```

```jyro
# Compare facet values between two persons
var roleFacet = GetFacetDefinitionByName("Role")
var person1Role = GetPersonFacetInstance(person1Id, roleFacet.id)
var person2Role = GetPersonFacetInstance(person2Id, roleFacet.id)

if person1Role != null and person2Role != null then
    if person1Role.values[0] == person2Role.values[0] then
        Log("Information", "Both persons have the same role")
    end
end
```

## Notes

- Returns null if the person is not found
- Returns null if the person exists but doesn't have an instance of the specified facet
- The `values` array contains the current values set on the facet instance
- Use `GetFacetDefinitionByName` to get the facet definition ID by name
