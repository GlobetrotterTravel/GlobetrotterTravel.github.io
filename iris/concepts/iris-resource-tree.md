---
layout: default
title: The Iris resource tree
parent: Concepts
has_children: false
has_toc: false
permalink: /iris/concepts/iris-resource-tree/
---

# The Iris resource tree

Iris lays out resources in a heirarchical manner. The full tree is shown below:

```
Global Level
└── PropertyDefinitions (global across all tenants, TenantId = Guid.AllBitsSet)

Tenant
├── PropertyValues (tenant-specific values for global PropertyDefinitions)
├── EntitlementDefinitions
├── FacetDefinitions  
├── FieldDefinitions (may reference → SetDefinitions)
├── HybridListDefinitions
│   └── HybridListDefinitionCategories
│       └── HybridListDefinitionItems
├── SetDefinitions
│   └── SetFilters (reference → FacetDefinitions)
└── Organizations (hierarchical: ParentOrganizationId creates org trees)
    ├── EntitlementInstances (reference → EntitlementDefinitions)
    ├── HybridListInstanceItems (reference → HybridListDefinitions + Categories)  
    ├── FacetValues (reference → FacetDefinitions)
    ├── FieldInstances (reference → FieldDefinitions)
    │   └── FieldInstanceItems (may reference other FieldInstanceItems via DependentOnValues)
    ├── Roles (organization-scoped)
    │   └── Permissions
    └── Persons
        ├── FacetValues (reference → FacetDefinitions)
        └── Roles (references to organization Roles)
```