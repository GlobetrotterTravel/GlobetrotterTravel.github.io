---
layout: default
title: Create an organization
parent: Organizations
grand_parent: Globetrotter Iris
nav_order: 10
permalink: /iris/organizations/create-an-organization/
---

# Create an organization
{: .no_toc }

Organizations can hold people and resource instances and may also hold child organizations.

> 🛈 To assign people, resources and facets to an organization, the organization must first be created and saved.

## Create the organization

1. Go to `Organizations`
2. Verify the organization does not already exist. Click `+ Add Organization`.

### Details tab

Go to the `Details` tab.

1. Enter a unique `Organization Number` and `Organization Name`
2. Enter an optional `Description`
3. If this organization belongs to another organization, select the `Parent Organization`.

> 🛈 Organizations can have at most one parent, but parent organizations can have multiple children to any depth. Circular hierarchies are not permitted.

4. Select the `Hybrid List Item Policy` which governs how hybrid list items are returned for this client.

- `Replace`: The shared hybrid list items are ignored, and only the organization's custom items are returned per category. Where an organization does not have any custom items in a category, the share items for that category only are returned.
- `Extend`: The shared hybrid list items are combined with the organization's custom items for the category. Where the organization's item names conflict, the organization's items will be returned instead of the shared items.

5. If the `organization` is not intended to be active, uncheck the `Active` box.

If the organization has a parent, and requires any inheritence, then go to the `Inheritence` tab.

Check the boxes that control selective inheritence for this child organization. Each box causes the selected resource on the child to be replaced with the one from the parent.

> 🛈 If the parent organization is itself a child, then inheritence is traced up the line until either the organization has no parents, or inheritence is turned off for that particular resource.

### Set facets

The remaining tabs display any defined facets grouped by their category. Set them appropriately.