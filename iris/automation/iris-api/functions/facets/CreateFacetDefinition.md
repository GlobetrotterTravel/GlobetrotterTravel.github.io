---
layout: default
title: CreateFacetDefinition
parent: Iris API functions
has_children: false
has_toc: false
---

# CreateFacetDefinition

Creates a new facet definition for a specified resource type.

## Syntax

```jyro
CreateFacetDefinition(resourceType, category, name, description, dataSensitivity)
CreateFacetDefinition(resourceType, category, name, description, dataSensitivity, options)
```

## Parameters

- **resourceType** (string): Target resource type - "Organization", "Person", "ServiceAccount", "FieldDefinition", "HybridListDefinition", or "EntitlementDefinition"
- **category** (string): Category name (max 100 chars)
- **name** (string): Facet name (max 100 chars)
- **description** (string): Description (max 500 chars)
- **dataSensitivity** (string): Data sensitivity level - "PII", "Confidential", "Internal", or "Public"
- **options** (object, optional): Additional configuration options

## Options Object Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| IsCritical | boolean | false | Whether this is a critical/required facet |
| AllowFreeText | boolean | false | Allow free-text values (not limited to predefined) |
| AllowMultipleValues | boolean | false | Allow multiple values to be selected |
| Values | array | [] | Array of predefined string values |
| EntitlementDefinitionId | string | null | Link to entitlement definition |
| HybridListDefinitionId | string | null | Link to hybrid list for values |
| FieldDefinitionId | string | null | Link to field definition |

## Returns

- **string**: The created facet definition ID (GUID), or `null` if creation failed

## Description

Creates a new facet definition that can be attached to the specified resource type. Facets are customizable metadata fields that extend the base entity properties. This function is useful for programmatically setting up tenant configurations or creating facets dynamically based on business rules.

## Examples

```jyro
# Create a simple facet with predefined values
var facetDefId = CreateFacetDefinition(
    "Person",
    "HR",
    "Employment Status",
    "Current employment status",
    "Internal",
    {
        "IsCritical": false,
        "AllowFreeText": false,
        "AllowMultipleValues": false,
        "Values": ["Full-time", "Part-time", "Contract", "Intern"]
    }
)

if facetDefId != null then
    Log("Information", "Created Employment Status facet: " + facetDefId)
end
```

```jyro
# Create a free-text, multi-value facet
var skillsFacetId = CreateFacetDefinition(
    "Person",
    "Professional",
    "Skills",
    "Technical and soft skills",
    "Internal",
    {
        "IsCritical": false,
        "AllowFreeText": true,
        "AllowMultipleValues": true,
        "Values": []
    }
)
```

```jyro
# Create a critical organization facet
var domainFacetId = CreateFacetDefinition(
    "Organization",
    "Technical",
    "Primary Domain",
    "Organization's primary email domain",
    "Confidential",
    {
        "IsCritical": true,
        "AllowFreeText": true,
        "AllowMultipleValues": false
    }
)
```

```jyro
# Create facet without options (all defaults)
var simpleFacetId = CreateFacetDefinition(
    "Organization",
    "General",
    "Notes",
    "General notes about the organization",
    "Internal"
)
```

```jyro
# Create PII-sensitive facet
var ssnFacetId = CreateFacetDefinition(
    "Person",
    "Government ID",
    "SSN Last Four",
    "Last four digits of Social Security Number",
    "PII",
    {
        "IsCritical": false,
        "AllowFreeText": true,
        "AllowMultipleValues": false
    }
)
```

## Data Sensitivity Levels

| Level | Description |
|-------|-------------|
| PII | Personally Identifiable Information - highest protection |
| Confidential | Business-sensitive data requiring protection |
| Internal | Internal use only, not for external sharing |
| Public | Can be shared publicly |

## Related Functions

- [GetFacetDefinitionByName](GetFacetDefinitionByName.md) - Retrieve facet definition by name
- [GetAllFacetDefinitions](GetAllFacetDefinitions.md) - Get all facet definitions
- [SetOrganizationFacetInstance](SetOrganizationFacetInstance.md) - Set facet values on organization
- [SetPersonFacetInstance](SetPersonFacetInstance.md) - Set facet values on person
