---
layout: default
title: Hybrid Lists
parent: Globetrotter Iris
nav_order: 50
has_children: true
has_toc: false
permalink: /iris/hybrid-lists/
---


# Hybrid Lists
{: .no_toc }

Hybrid lists provide a way to manage categorized lists of items that can be both globally standardized and organization-specific. This enables you to maintain consistent options across organizations while allowing individual organizations to customize those options for their needs.

Hybrid lists combining system-wide definitions with organization-level customization, controlled by a policy that determines how items are merged.

Hybrid lists operate on a three-tier hierarchy:

| Tier | Scope | Contains | Managed By |
|------|-------|----------|------------|
| Definition | Tenant-wide | Categories and standard items | Tenant Administrator |
| Instance | Organization | Reference to definition, facet values | Tenant Administrator |
| Custom Items | Organization | Organization-specific items | Tenant Administrator |

## Table of contents
{: .no_toc }

1. TOC
{:toc }

### Definitions

A hybrid list definition is created at the tenant level and shared by all organizations. Definitions contain:

- **Categories**: Logical groupings for items (e.g., "Europe", "Asia", "Americas" for a countries list)
- **Definition Items**: The standard items within each category (e.g., "France", "Germany", "United Kingdom")
- **Facet Definitions**: Optional metadata that can be attached to instances

For example, a "Countries" hybrid list definition might have:

Countries (Definition)
├── Europe (Category)
│   ├── France
│   ├── Germany
│   └── United Kingdom
├── Asia (Category)
│   ├── Japan
│   └── China
└── Americas (Category)
    ├── United States
    └── Canada

### Instances and Custom Items

When an organization uses a hybrid list, it has:

- A hybrid list instance: A reference to the definition, with optional facet values
- Custom items: Organization-specific items that are added to or replace the definition items

The key design principle is that definition items are not copied to organizations. Instead, they are merged dynamically based on the organization's policy. This means updates to definition items are immediately reflected across all organizations.

## The Hybrid List Item Policy

Each organization has a HybridListItemPolicy that controls how definition items and custom items are combined. This policy applies to all hybrid lists for that organization.

### Replace Policy

With the Replace policy, only the organization's custom items are shown. Definition items are hidden.

Definition Items:    [France, Germany, UK, Japan, China, USA, Canada]
Organization Items:  [CustomRegion1, CustomRegion2]
─────────────────────────────────────────────────────────────────────
Visible Items:       [CustomRegion1, CustomRegion2]

Use Replace when:
- The organization wants a completely customized list
- Standard options are not relevant to this organization
- A smaller, focused list is preferred

### Extend Policy

With the Extend policy, definition items and custom items are combined. Both are visible.

Definition Items:    [France, Germany, UK, Japan, China, USA, Canada]
Organization Items:  [CustomRegion1, CustomRegion2]
─────────────────────────────────────────────────────────────────────
Visible Items:       [France, Germany, UK, Japan, China, USA, Canada, CustomRegion1, CustomRegion2]

Use Extend when:
- The organization wants standard options plus their own additions
- Consistency with the tenant-wide list is important
- The organization has additional internal codes or categories

## Facets on Hybrid Lists

Hybrid list definitions can have facet definitions associated with them. These become available as facet instances on the organization's hybrid list instance, allowing you to attach metadata to how an organization uses a particular hybrid list.

For example, a "Regions" hybrid list might have a "Primary Focus" facet. Organization A could set their Primary Focus to "EMEA", while Organization B sets theirs to "APAC". This metadata is stored on the instance, separate from the items themselves.

## Limits

The following limits apply:

| Resource | Maximum |
|----------|---------|
| Definitions per tenant | 100 |
| Categories per definition | 50 |
| Items per definition | 200 |
| Custom items per organization | 1,000 |
| Item name length | 100 characters |
| Item value length | 500 characters |