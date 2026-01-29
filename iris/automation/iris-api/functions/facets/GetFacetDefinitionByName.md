---
layout: default
title: GetFacetDefinitionByName
parent: Iris API functions
has_children: false
has_toc: false
---

# GetFacetDefinitionByName

Retrieves a facet definition by its name (case-insensitive).

## Syntax

```jyro
GetFacetDefinitionByName(name)
```

## Parameters

- **name** (string): The name of the facet definition to retrieve

## Returns

- **object**: Facet definition object with { id, name, category, allowFreeText, allowMultipleValues, values, isActive }, or null if not found

## Description

Looks up a facet definition by name using case-insensitive matching. This is useful for getting the facet definition ID without hardcoding GUIDs in scripts.

## Examples

```jyro
# Get facet definition and use its ID
var facetDef = GetFacetDefinitionByName("person-skills")
if facetDef != null then
    var skills = GetPersonFacetInstance(Data.Person.Id.Value, facetDef.id)
end
```

```jyro
# Set facet using name instead of hardcoded GUID
var skillsDef = GetFacetDefinitionByName("person-skills")
if skillsDef != null then
    SetPersonFacetInstance(Data.Person.Id.Value, skillsDef.id, ["C#", "TypeScript"])
end
```

```jyro
# Check if facet definition exists before using
var regionDef = GetFacetDefinitionByName("organization-region")
if regionDef == null then
    Log("Warning", "Region facet definition not found")
    return
end
```

```jyro
# Access facet definition properties
var departmentDef = GetFacetDefinitionByName("department")
if departmentDef != null then
    Log("Information", "Facet: " + departmentDef.name)
    Log("Information", "Allow multiple: " + departmentDef.allowMultipleValues)
    Log("Information", "Predefined values: " + Length(departmentDef.values))
end
```
