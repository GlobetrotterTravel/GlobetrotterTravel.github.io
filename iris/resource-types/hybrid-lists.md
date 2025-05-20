---
layout: default
title: Hybrid Lists
parent: Resource Types
has_children: false
has_toc: false
permalink: /iris/resource-types/hybrid-lists/
---

# Hybrid lists
{: .no_toc }

A hybrid list is a categorised list of items that is returned for all organizations. Hybrid lists give every organisation access to a common, categorised set of values while still allowing each organisation to introduce its own local additions. Globally defined items and categories are created once by the tenant administrator; organisations may then add items of their own but cannot alter the underlying structure. When an organisation requests a hybrid list, Iris merges the global items with any organisation-specific items and returns a single, "effective" view that respects the organisation's chosen item-merge policy.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Global hybrid lists

A global hybrid list is created at the tenant level and is identified by a unique name. Each list contains one or more categories, and each category contains one or more items. Any number of hybrid lists, categories and items can be defined. 

Within a given list every category name must be unique, and within a given category every item name must be unique. Those names do not have to be unique outside that scope: two different hybrid lists can share the same category name, and two categories can each contain an item that happens to have the same name.

## Organization-specific hybrid lists

Each organization automatically inherits a copy of each global hybrid list. The organisation may add new items to any of the predefined categories, giving each item its own name, value and description. An organisation cannot create additional categories, remove existing ones or modify the globally defined items themselves; it can only supplement what is already there. Item names introduced by the organisation must be unique within the category from the organisation's viewpoint. If the organisation uses a name that already exists for a global item in that category, the organisation's item overlays the global one and is the version that is returned.

Whether the organisation's items replace or extend the global set is controlled by the organisation's hybrid-list item policy. When the policy is set to Replace, Iris hides the global items entirely and returns only the organisation's additions, unless the organization has no additional items for that category in which case the global items are returned. When the policy is set to Extend, Iris returns the organisation's items first and then appends any remaining global items that were not overridden. This policy is configured once per organisation and applies to every hybrid list the organisation consumes.

When a client system requests a hybrid list for an organisation, the service applies the policy, merges the items and returns the list in the order in which the categories were defined. Inside each category the organisation's items appear ahead of any unchanged global items. If an organisation wants to suppress a single global item it must override that item's name with its own entry; the platform does not offer a separate "hide" flag.

## Outcome Matrix - Organization Level

| Organization Items | Policy | Result |
|-------------------|--------|--------|
| None at all | Any | Global items only (all categories) |
| Has some items | "Replace" | Org items only where available; global items for categories with no org items |
| Has some items | "Extend" | Org items + non-overridden global items where available; global items for categories with no org items |

## Outcome Matrix - Per Category

| Category Has Org Items | Policy | Result for This Category |
|------------------------|--------|--------------------------|
| No | Any | Global items only |
| Yes | "Replace" | Organization items only (hides all global items in this category) |
| Yes | "Extend" | Organization items first, followed by non-overridden global items |