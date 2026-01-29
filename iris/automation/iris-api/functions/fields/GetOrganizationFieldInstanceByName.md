---
layout: default
title: GetOrganizationFieldInstanceByName
parent: Iris API functions
has_children: false
has_toc: false
---

# GetOrganizationFieldInstanceByName

Retrieves a field instance from an organization by field definition name.

## Syntax

```jyro
GetOrganizationFieldInstanceByName(organizationId, fieldDefinitionName)
```

## Parameters

- **organizationId** (string): The GUID of the organization
- **fieldDefinitionName** (string): The name of the field definition (case-insensitive)

## Returns

- **object**: Field instance object, or null if not found

The returned object contains:
- **id** (string): The GUID of the field instance
- **fieldDefinitionId** (string): The GUID of the field definition
- **label** (string or null): The label of the field
- **description** (string or null): The description
- **hint** (string or null): The hint shown to consultants
- **order** (number): The display order
- **fieldRequirementLevel** (string): "Required", "Optional", or "Hidden"
- **regexPattern** (string or null): Validation regex
- **allowFreeTextOverride** (boolean): Whether free text is allowed
- **items** (array): Array of field instance items, each with:
  - **id** (string): Item GUID
  - **name** (string): Item name
  - **value** (string): Item value
  - **description** (string or null): Item description

## Description

Finds a field instance by the name of its field definition. This is a convenient way to access field data without knowing the definition's GUID.

## Examples

```jyro
# Get a specific field instance by name
var costCenterField = GetOrganizationFieldInstanceByName(
    Data.Organization.Id,
    "Cost Center"
)

if costCenterField != null then
    Log("Information", "Organization has Cost Center field")
    Log("Information", "Items: " + costCenterField.items.length)

    foreach item in costCenterField.items do
        Log("Information", "  " + item.name + " = " + item.value)
    end
else
    Log("Information", "Organization does not have Cost Center field")
end
```

```jyro
# Validate required field has values
var regionField = GetOrganizationFieldInstanceByName(
    Data.Organization.Id,
    "Region"
)

if regionField == null or regionField.items.length == 0 then
    Log("Warning", "Organization missing required Region field")
    CancelAction("Region field is required")
end

Log("Information", "Region: " + regionField.items[0].value)
```

```jyro
# Extract field values for processing
var departmentField = GetOrganizationFieldInstanceByName(orgId, "Department")

if departmentField != null then
    var departments = []
    foreach item in departmentField.items do
        departments.add(item.value)
    end

    Log("Information", "Departments: " + departments.join(", "))
end
```

```jyro
# Check if field allows free text override
var customField = GetOrganizationFieldInstanceByName(
    Data.Organization.Id,
    "Custom Options"
)

if customField != null then
    if customField.allowFreeTextOverride then
        Log("Information", "Users can add custom options to this field")
    else
        Log("Information", "Field is restricted to predefined options only")
    end
end
```

```jyro
# Copy field values to another system
var payrollCodeField = GetOrganizationFieldInstanceByName(orgId, "Payroll Code")

if payrollCodeField != null and payrollCodeField.items.length > 0 then
    # Get the first (primary) value
    var primaryCode = payrollCodeField.items[0].value
    var primaryName = payrollCodeField.items[0].name

    Log("Information", "Syncing payroll code: " + primaryCode + " (" + primaryName + ")")

    # Use in other operations...
end
```

```jyro
# Check multiple fields for compliance
var hasAllRequiredFields = true
var missingFields = []

var requiredFieldNames = ["Cost Center", "Region", "Department"]

foreach fieldName in requiredFieldNames do
    var field = GetOrganizationFieldInstanceByName(orgId, fieldName)
    if field == null or field.items.length == 0 then
        hasAllRequiredFields = false
        missingFields.add(fieldName)
    end
end

if not hasAllRequiredFields then
    Log("Warning", "Missing required fields: " + missingFields.join(", "))
end
```

## See Also

- [GetAllOrganizationFieldInstances](GetAllOrganizationFieldInstances.md) - Get all field instances
- [SetOrganizationFieldInstance](SetOrganizationFieldInstance.md) - Set field items
- [GetAllFieldDefinitions](GetAllFieldDefinitions.md) - Get all field definitions
