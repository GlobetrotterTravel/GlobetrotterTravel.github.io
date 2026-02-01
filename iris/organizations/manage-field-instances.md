---
layout: default
title: Manage field instances
parent: Organizations
grand_parent: Globetrotter Iris
nav_order: 30
permalink: /iris/organizations/manage-field-instances/
---

# Manage field instances
{: .no_toc }

Organizations can have attached field instances which set up a structure for capturing transactional information that is related to the organization.

## Create a new field instance

1. Go to `Organizations`
2. Locate the organization to which you would like to add a new field instance.
3. In the `Actions` column, click `Manage Field Instances`. The `Field Instances` page opens.
4. Click `+ Add Field Instance` and select the `field definition` that the instance will be derived from.

> 🛈 Each organization can have at most one field instance per field definition.

5. Click `Create Instance`. The instance is created.

## Edit the field instance

### Properties tab

Go to the `Properties` tab.

1. In the `Actions` column, click `Edit`.
2. Provide the field instance `Name`, `Description` and `Hint` that should be displayed when using this field instance.
3. Set a `Display Order`. When using multiple fields, it can help to display them in a certain order. Field instances are returned with lower order fields returned first.
4. Set the `Requirement Level` for this field:

- `Not Used`: The field instance is configured but not being used currently
- `Optional`: The field instance is displayed but lack of a value does not halt processing.
- `Mandatory`: The process should halt if a value is not provided.
- `Auto Populated`: The field is mandatory but a value is not required to be provided by a human. Instead a downstream process will populate the value.

5. Set a regex pattern if required.

6. If the field should accept free text, check the `Allow Free Text Override` checkbox.

### Items tab

If the field should provide items for selection, go to the `Items` tab.

#### Add an item

To add a new item,

1. Click the `+ Add Item` button. A card with a form appears.
2. Populate the item's `Name`, `Value` and `Description`
3. Repeat for each additional item

#### Remove an item

To remove an item, locate it in the list and click the `Garbage Can` icon. The item is removed.

### Dependencies tab

If the field has item selections that should depend on item selections in other fields, go to the `Dependencies` tab.

> 🛈 You must have at least two field instances, with at least one item each for the current organization to use field dependencies.

To configure field dependencies,

1. Select the field instance upon which this field should depend
2. For each item in this field, select which upstream items should make it visible.

> 🛈 Items with no selection are always visible.

### Set facets

The remaining tabs display any defined facets grouped by their category. Set them appropriately.