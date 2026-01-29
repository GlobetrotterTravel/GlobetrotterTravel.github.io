---
layout: default
title: Create a field definition
parent: Fields
grand_parent: Globetrotter Iris
nav_order: 10
permalink: /iris/fields/create-a-field-definition/
---

# Create a field definition
{: .no_toc }

Creating a field definition allows organizations to create a field instance that is derived from the field defintion, with per-organization customizations.

## Create a new field definition

To create a new field definition, open the `Definitions` navigation menu and click `Field Definitions`, then click `+ Create Field Definition`.

The `Create Field Definition` panel appears.

### Details tab

Populate details:

1. `Name`: The identifier for this field. It must be unique across all field definitions and should not contain spaces or punctuation. Follow your data collection standards for column/field naming.
2. `Label`: The friendly display label for this field. This is normally used in downstream systems to display the field on a form and is used if a derived field instance does not specify its own label.
3. `Category`: The logical category for the field. This is useful to separate fields into logical groupings.
4. `Description`: A short description of the intended purpose of this field.
5. `Max Length`: The maximum number of characters this column can hold. This value should be aligned with the database that will be used to persist values collected by this field.
6. `Default Order`: The default ordering for instances derived from this field. Fields are intended to be ordered ascending. It's recommended that this value be set to a multiple of ten to allow for interpolation of new fields should requirements change.

### Settings tab

If this field should be bound to a set, then select it in the `Select Set Definition` dropdown. See the section on sets for more information.

Click `Save`. The field definition is created.