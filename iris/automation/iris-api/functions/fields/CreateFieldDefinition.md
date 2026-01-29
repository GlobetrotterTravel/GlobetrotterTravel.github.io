---
layout: default
title: CreateFieldDefinition
parent: Iris API functions
has_children: false
has_toc: false
---

# CreateFieldDefinition

Creates a new field definition for custom data fields.

## Syntax

```jyro
CreateFieldDefinition(category, name, label, description)
CreateFieldDefinition(category, name, label, description, options)
```

## Parameters

- **category** (string): Category name (max 100 chars)
- **name** (string): Internal field name (max 100 chars)
- **label** (string): Display label (max 100 chars)
- **description** (string): Description (max 500 chars)
- **options** (object, optional): Additional configuration options

## Options Object Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| MaxLength | number | 1000 | Maximum allowed character length (1-10000) |
| DefaultOrder | number | 0 | Sort order for display purposes |
| SetDefinitionId | string | null | ID of a set definition for dropdown values |

## Returns

- **string**: The created field definition ID (GUID), or `null` if creation failed

## Description

Creates a new field definition that defines a custom text field for resources. Field definitions specify the metadata for fields that can be populated on organizations, persons, or other entities. Optional set definitions can be linked to provide dropdown selection values.

## Examples

```jyro
# Create a simple text field
var fieldDefId = CreateFieldDefinition(
    "Personal Information",
    "email",
    "Email Address",
    "Primary email address for contact",
    {
        "MaxLength": 255,
        "DefaultOrder": 10
    }
)

if fieldDefId != null then
    Log("Information", "Created email field: " + fieldDefId)
end
```

```jyro
# Create a field with dropdown values from a set definition
var fieldDefId = CreateFieldDefinition(
    "Address",
    "country",
    "Country",
    "Country of residence",
    {
        "MaxLength": 100,
        "DefaultOrder": 20,
        "SetDefinitionId": "12345678-1234-1234-1234-123456789012"
    }
)
```

```jyro
# Create a short text field (username)
var fieldDefId = CreateFieldDefinition(
    "Profile",
    "username",
    "Username",
    "Unique username for the system",
    {
        "MaxLength": 50,
        "DefaultOrder": 5
    }
)
```

```jyro
# Create field with default options
var notesFieldId = CreateFieldDefinition(
    "General",
    "notes",
    "Notes",
    "General notes and comments"
)
```

```jyro
# Create multiple address fields
var addressFields = [
    { "name": "street", "label": "Street Address", "order": 1 },
    { "name": "city", "label": "City", "order": 2 },
    { "name": "state", "label": "State/Province", "order": 3 },
    { "name": "postalCode", "label": "Postal Code", "order": 4 }
]

foreach field in addressFields do
    var id = CreateFieldDefinition(
        "Address",
        field.name,
        field.label,
        field.label + " for mailing address",
        {
            "MaxLength": 200,
            "DefaultOrder": field.order
        }
    )
    Log("Information", "Created field " + field.name + ": " + id)
end
```

## Validation Rules

- **MaxLength** must be between 1 and 10000
- **name** should be unique within the tenant (case-insensitive)
- **SetDefinitionId** must reference a valid set definition if provided

## Related Functions

- [GetAllFieldDefinitions](GetAllFieldDefinitions) - Get all field definitions
- [SetOrganizationFieldInstance](SetOrganizationFieldInstance) - Set field values on organization
