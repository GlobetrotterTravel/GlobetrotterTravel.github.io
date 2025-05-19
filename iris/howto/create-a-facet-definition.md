---
layout: default
title: Create a facet definition
parent: Iris
nav_order: 10
permalink: /iris/howto/create-a-facet-definition/
---

# Create a facet definition

To create a new facet definition, first decide:

Does the data need to hold multiple values?
Does the data need to allow free text values?

The answers to these questions determine what type of facet to create. For further guidance see: Facets

## Create the facet definition

In the navigation pane, click `Facet Definitions`.

Click the `New` icon. A panel appears.

In `Target Item Type`  choose the type of item this facet should appear on:

* Organization
* Person
* Field
* Entitlement

Set the `Category` to the text of the tab the facet should appear on. Take care to match the category name to an existing category exactly, or choose a new category name. A new tab is presented in the item's management page for each category of facet that is assigned to that item type.

Set the `Name` and `Description` of the facet.

Choose the facet's `Data Sensitivity`:

* **PII**: The facet values hold Personally Identifiable Information
* **Confidential**: The facet values hold information that should only be shared on a need-to-know basis
* **Internal**: The facet values hold information that should not be shared outside of the service provider
* **Public**: The facet values are unlikely to contain information that would cause harm if known publicly.

## Add value(s)

This step is only required if your facet should present a list of values when being populated. If your facet should only accept free text values, skip this step.

Navigate to the `Values` tab.

Click the `Value` text box and type a value that should be available for this facet.

Click the `Add` button. The value is added.

To remove a value, select it in the list and click the `Remove` button. To rename, select the value in the list and click the `Rename` button.

Items can be moved up and down by selecting the value in the list and clicking the `Move Up` and `Move Down` buttons.

## Configure facet settings

Navigate to the `Settings` tab.

Ensure that the facet's `Active` switch is on. Inactive facets do not appear in the management pages, nor can they have values assigned or read from them.

Set the `Is Critical` flag appropriately. A `critical facet` is one that performs an important role in a business process and therefore should be populated. Subscribers can check `critical facets` for appropriate values and halt processing if required. Iris will warn if a record is saved with unpopulated values in `critical facets`. Iris does not prevent page saves with unpopulated critical facets however.

If the facet needs to allow a free text value to be saved, then switch on `Allow Free Text`. If this switch is off, then only the values provided in the `Values` tab will be allowed to be saved.

If the facet needs to store more than one value per item, then switch on `Allow Multiple Values`. When the switch is off, only a single value may be assigned at any one time.

## Save the facet

Click `Save`. You are returned to the `Facet Definitions` page and a confirmation message appears.