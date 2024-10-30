# Globetrotter Iris

Globetrotter Iris is a scalable, flexible and reliable solution to managing service configuration data for organisations of any size. Iris is an API-first platform that is designed to fit into your workflows and deliver the benefits of centralised control and audit of complex information.

Iris serves *your data your way* to `subscriber` systems, giving you the confidence that all parts of your infrastructure are seeing the same source of truth. `Subscribers` can access a little or a lot, based on a strong security model and granular `permissions`. Integrate with both internal systems as well as partners and customers, taking advantage of industry REST API standards.

## In this section

* [Jump to](#jump-to)
* [Overview and key concepts](#overview-and-key-concepts)
    * [Metadata-aware structures](#metadata-aware-structures)
## Jump to

The following sections detail how to administer the different parts of Iris.

- [Getting Started](Getting%20Started.html)
- [System Journal](System%20Journal.html)
- [Properties](Properties.html)
- [Users, Roles and Permissions](Users,%20Roles%20and%20Permissions.html)
- [Entities](Entities.html)
- [Facets](Facets.html)
- [Entitlements](Entitlements.html)
- [Fields](Fields.html)
- [Hybrid Lists](Hybrid%20Lists.html)
- [Integration with Iris](Integration%20with%20Iris.html)

There is also a [Glossary of Terms](Glossary.html) available.

## Overview and key concepts

### Object types

The term *object* refers in general to any logical business data structure managed by Iris. Objects generally have a name or label to identify them, and belong to an `object type`.

### Extensible and non-extensible objects 

Objects may be `extensible` or `non-extensible`. An `extensible object` is one that that supports the creation of:

* Classes, and therefore,
* Facet schemas that are assigned to those classes.

`Extensible objects` may be thought of as the primary "things" that are managed by Iris.

`Non-extensible object` do not allow facet schema assignment. They may be thought of as things that exist to support `extensible objects`.

The complete list of `extensible object` `types` is below:

* Entity
* Field
* Hybrid List
* Entitlement

The complete list of `non-extensible objects` is below:

* Category
* Item
* Facet

# Object classes 

Administrators can create `classes` for `Extensible object` `types`, to manage `facet schemas` as a group. Grouping 

# Facets 

`Instances` of `extensible objects` will make the `facet schema` available for object-specific values to be recorded within the `facet schema`.








The following `objects` are `extensible`, meaning that `classes` created in them can have `facet schemas` assigned to them:

* Entity
* Field
* Hybrid List
* Entitlement


The following structures are non-extensible, meaning that no facet data may be recorded against them.

* Category
* Item
 
