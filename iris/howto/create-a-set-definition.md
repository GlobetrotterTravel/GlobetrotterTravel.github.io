---
layout: default
title: Create a set definition
parent: How-to guides
permalink: /iris/howto/create-a-set-definition/
---

# Create a set definition

A `set` can be created to automatically filter a `organizations` or `persons` according to predefined facet criteria.

## Create the set definition

In the navigation pane, click `Set Definitions`.

Click the `New` icon. A panel appears.

Set the `Name` and `Description` for the `set`.

Ensure that the `Active` switch is on.

In `Target Item Type` choose the type of item this set is for:

* Organization
* Person

## Add filters

Click the `Add Filter` button.

A `facet` select box and `value` text field appears.

Choose the `Facet` to search on and type the value to match. Records of `Target Item Type` with `facet` values matching the value here will be returned in the `set`.

{: .note-title }
The value to search is case-insensitive but must otherwise match exactly one of the facet values of the item type the set will search.

You can add more filters by clicking the `Add Filter` button and selecting further facets and values.

{: .note-title }
The `facet` criteria are additive (OR): records will be returned in a union of all searches across the `facets` specified.

## Save the set

Click `Save`. You are returned to the `Set Definitions` page and a confirmation message appears.