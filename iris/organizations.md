---
layout: default
title: Organizations
parent: Globetrotter Iris
nav_order: 10
has_children: true
has_toc: false
permalink: /iris/organizations/
---

# Organizations
{: .no_toc }

Organizations represent the entities that the MSP partners with. By keeping track of organizations and their properties, MSPs can better serve their clients, work better with their vendors and ensure maximum value creation across the ecosystem.

In Iris, organizations are the focal point of the other resource types: people, fields, hybrid lists and entitlements. By assigning instances of these resources to organizations, and setting organization facets, a complete digital representation of each company can be built.

## Configurations

Organizations can be assigned the following resources:

- **facet instances**: zero or one per facet definition
- **field instances**: zero or one per field definition
- **entitlements**: zero or one per hybrid list definition
- **hybrid lists**: exactly one per hybrid list definition
- **people**: unlimited.

Each organization is identified by a unique **Organization Number** and a unique **Organization Name**, which is normally the organization's trading name. An optional **Description** can provide additional context about the organization.

Organizations can be arranged hierarchically by specifying a **Parent Organization**, allowing you to model corporate structures and relationships. The **Hybrid List Item Policy** controls how the organization's custom hybrid lists interact with global lists—either `Replace` to override global items entirely, or `Extend` to add to them.

## Inheritence

Additionally, organizations can have granular inheritence configurations for their resources. When a parent is set, an organization can optionally inherit any combination of:

- field instances
- entitlements
- facets
- hybrid lists
- people

> 🛈 Inheritence doesn't apply if the organization does not have a parent.

Inheritence applies to parents of parents too. If we use a three-organization heirarchy A->B->C, where C inherits from B, and B inherits from A, then organization C will return organization A's configuration for the resource type for which inheritence is enabled.

This inheritence model allows fine-grained control of organization heierarchies while reducing boilerplate data entry for organization collections.

## People

When a person is created, they must be assigned to an organization. Each organization therefore has a collection of people assigned to it, that can be managed as a group. Each person may belong to multiple organizations.

Organization membership controls other things like the items that person-based sets return. 

> 🛈 Each person must be assigned to at least one organiztion.

## Field Instances

Each field instance for an organization is derived from a single field definition. Organizations can have at most one field instance per definition, up to the maximum number of field definitions available.

The field instance is therefore specific to that particular organization, and customizations to the field instance are not shared to other organizations.

Field instances are flexible, allowing modification of the field's label, description, hint for user interfaces, display order, validation & requirement for data capture, as well as the items contained in the field instance and whether free text is allowed. Downstream systems can use these properties to configure deterministic behaviour on user forms and other data capture behaviours, whilst enforcing centralised data management practises through the use of field definition names.

## In this section

- [Create an organization](/iris/organizations/create-an-organization/)
- [Delete an organization](/iris/organizations/delete-an-organization/)
- [Manage field instances](/iris/organizations/manage-field-instances/)
- [Configure organization hybrid list](/iris/organizations/configure-organization-hybrid-list/)
- [Create an entitlement instance](/iris/organizations/create-entitlement-instance/)
