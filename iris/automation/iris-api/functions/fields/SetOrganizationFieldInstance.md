---
layout: default
title: SetOrganizationFieldInstance
parent: Iris API functions
has_children: false
has_toc: false
---

# SetOrganizationFieldInstance

Sets or updates field instance items and configuration on an organization.

## Syntax

```jyro
SetOrganizationFieldInstance(organizationId, fieldDefinitionId, items)
SetOrganizationFieldInstance(organizationId, fieldDefinitionId, items, options)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **fieldDefinitionId** (string): The GUID of the field definition
- **items** (array): Array of item objects with the following properties:
  - **name** (string): The name/label of the item (required)
  - **value** (string): The value of the item (required)
  - **description** (string|null): Optional description for the item
  - **dependentOnValues** (array|null): Optional array of parent item GUIDs this item depends on
- **options** (object|null): Optional configuration object with the following properties:
  - **label** (string): Display label for the field (max 100 chars)
  - **description** (string): Description of the field (max 500 chars)
  - **hint** (string): Hint shown to consultants (max 200 chars)
  - **order** (number): Display order in the form (default: 0)
  - **fieldRequirementLevel** (string): One of "NotUsed", "Optional", "Mandatory", or "AutoPopulated" (default: "Optional")
  - **regexPattern** (string): Validation regex pattern (max 500 chars)
  - **dependentOnFieldId** (string): GUID of the parent field this field depends on
  - **allowFreeTextOverride** (boolean): Whether free text input is allowed (default: false)

## Returns

- **boolean**: true if successful, false otherwise

## Description

Smart detection: Works in both "Before" hooks (in-memory) and "After" hooks (persisted):
- If organization exists in database: Fetches, modifies, and saves
- If organization is in-memory only: Modifies Data.Organization in execution context

Creates a new field instance if one doesn't exist for the given definition, or replaces all items in the existing instance.

**Options Handling**:
- When creating a new field instance, all provided options are applied
- When updating an existing field instance, only the explicitly provided options are updated (other properties remain unchanged)
- Options not provided use default values for new instances

**Clearing Items**: Passing an empty array (`[]`) will clear all items from an existing field instance. If the field instance doesn't exist and no options are provided, passing an empty array returns true without creating a new instance.

## Examples

### Basic Usage

```jyro
# Get field definition and set items with default options
var fieldDefs = GetAllFieldDefinitions()
var countryField = fieldDefs[0]

SetOrganizationFieldInstance(
    Data.Organization.Id.Value,
    countryField.id,
    [
        {name: "United States", value: "US", description: "North America"},
        {name: "Canada", value: "CA", description: "North America"},
        {name: "Mexico", value: "MX", description: "North America"}
    ]
)
```

### With Configuration Options

```jyro
# Set field items with full configuration
var priorityField = GetAllFieldDefinitions().filter(f => f.name == "Priority")[0]

if priorityField != null then
    SetOrganizationFieldInstance(
        Data.Organization.Id.Value,
        priorityField.id,
        [
            {name: "High", value: "1", description: "Critical items"},
            {name: "Medium", value: "2", description: "Normal priority"},
            {name: "Low", value: "3", description: "Can wait"}
        ],
        {
            label: "Task Priority",
            description: "Select the priority level for this task",
            hint: "High priority items are processed first",
            order: 10,
            fieldRequirementLevel: "Mandatory",
            allowFreeTextOverride: false
        }
    )
    Log("Information", "Priority field configured for organization")
end
```

### Partial Options Update

```jyro
# Update only specific options on an existing field instance
var statusField = GetAllFieldDefinitions().filter(f => f.name == "Status")[0]

if statusField != null then
    SetOrganizationFieldInstance(
        Data.Current.Id.Value,
        statusField.id,
        [
            {name: "Active", value: "active"},
            {name: "Inactive", value: "inactive"},
            {name: "Pending", value: "pending"}
        ],
        {
            label: "Account Status",
            fieldRequirementLevel: "Optional"
            # Only label and requirement level are updated
            # Other properties (hint, order, etc.) remain unchanged
        }
    )
end
```

### With Regex Validation

```jyro
# Set a field with regex pattern validation
var emailDomainField = GetAllFieldDefinitions().filter(f => f.name == "Email Domain")[0]

if emailDomainField != null then
    SetOrganizationFieldInstance(
        Data.Organization.Id.Value,
        emailDomainField.id,
        [
            {name: "Company Domain", value: "@company.com"},
            {name: "Partner Domain", value: "@partner.com"}
        ],
        {
            label: "Allowed Email Domains",
            hint: "Select or enter a valid email domain",
            regexPattern: "^@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$",
            allowFreeTextOverride: true
        }
    )
end
```

### Dependent Field Configuration

```jyro
# Set up a field that depends on another field
var countryField = GetAllFieldDefinitions().filter(f => f.name == "Country")[0]
var cityField = GetAllFieldDefinitions().filter(f => f.name == "City")[0]

if countryField != null and cityField != null then
    # First, set up the country field
    SetOrganizationFieldInstance(
        Data.Organization.Id.Value,
        countryField.id,
        [
            {name: "United States", value: "US"},
            {name: "Canada", value: "CA"}
        ],
        {
            label: "Country",
            order: 1,
            fieldRequirementLevel: "Mandatory"
        }
    )

    # Then set up the city field as dependent on country
    SetOrganizationFieldInstance(
        Data.Organization.Id.Value,
        cityField.id,
        [
            {name: "New York", value: "NYC"},
            {name: "Los Angeles", value: "LA"},
            {name: "Toronto", value: "TOR"},
            {name: "Vancouver", value: "VAN"}
        ],
        {
            label: "City",
            order: 2,
            fieldRequirementLevel: "Mandatory",
            dependentOnFieldId: countryField.id
        }
    )
end
```

### In BeforeOrganizationCreated Hook

```jyro
# Set default field configuration when organization is created
var regionField = GetAllFieldDefinitions().filter(f => f.name == "Region")[0]

if regionField != null then
    SetOrganizationFieldInstance(
        Data.Organization.Id.Value,
        regionField.id,
        [
            {name: "EMEA", value: "emea", description: "Europe, Middle East & Africa"},
            {name: "APAC", value: "apac", description: "Asia Pacific"},
            {name: "AMER", value: "amer", description: "Americas"}
        ],
        {
            label: "Operating Region",
            hint: "Select the primary region for this organization",
            order: 5,
            fieldRequirementLevel: "Mandatory"
        }
    )
    Log("Information", "Default region options configured for new organization")
end
```

### Clear Items While Preserving Configuration

```jyro
# Clear all items but keep the field instance configuration
var statusField = GetAllFieldDefinitions().filter(f => f.name == "Status")[0]

if statusField != null then
    var success = SetOrganizationFieldInstance(
        Data.Organization.Id.Value,
        statusField.id,
        []  # Empty array clears all items
    )

    if success then
        Log("Information", "Cleared all status options from organization")
    end
end
```

### Create Field Instance Without Items

```jyro
# Create a field instance with configuration but no initial items
var notesField = GetAllFieldDefinitions().filter(f => f.name == "Notes")[0]

if notesField != null then
    SetOrganizationFieldInstance(
        Data.Organization.Id.Value,
        notesField.id,
        [],  # No items
        {
            label: "Additional Notes",
            description: "Free-form notes field",
            hint: "Enter any additional information",
            fieldRequirementLevel: "Optional",
            allowFreeTextOverride: true
        }
    )
end
```

## Field Requirement Levels

| Value | Description |
|-------|-------------|
| `NotUsed` | The field is not used by the organization |
| `Optional` | The field is non-mandatory (default) |
| `Mandatory` | The field is required and a missing value will prevent further processing |
| `AutoPopulated` | The field is mandatory but is populated by a downstream process or system |

## Notes

- The field definition must exist in the tenant before calling this function
- Invalid items (missing name or value) are skipped with a warning
- If items are provided but none are valid, the function returns false
- Passing an empty array clears all items from an existing field instance
- If the field instance doesn't exist and an empty array is passed with no options, returns true (nothing to create)
- If the field instance doesn't exist and options are provided with an empty array, a new field instance is created with the options
- Aggregate IDs (Id, TenantId) are protected and cannot be modified by scripts
- Each item gets a new auto-generated ID when created
- Invalid `fieldRequirementLevel` values are logged as warnings and default to "Optional"
- Invalid `dependentOnFieldId` GUID format is logged as a warning and ignored
- The `regexPattern` is validated to ensure it's a valid regular expression
