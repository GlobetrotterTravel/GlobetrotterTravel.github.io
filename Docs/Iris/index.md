# Globetrotter Iris

Globetrotter Iris is a scalable, flexible and reliable solution to managing service configuration data for organisations of any size. Iris is an API-first platform that is designed to fit into your workflows and deliver the benefits of centralised control and audit of complex information.

Iris manages objects called `components`, which when combined in a structured manner, represent the service configuration for all of your clients and partners. The principle `component` is `entity`, to which all other `components` plug into.

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
- [Assets](Assets.htm)
- [Fields](Fields.html)
- [Hybrid Lists](Hybrid%20Lists.html)
- [Integration with Iris](Integration%20with%20Iris.html)

There is also a [Glossary of Terms](Glossary.html) available.

## Overview and key concepts

## Components

The following are Iris components:

* Entity
* Field
* Hybrid List
* Entitlement
* Asset

Administrators configure `component` `classes`, which represent the layout that is needed when new instances of these `components` are created.

Classes 

Components are configured according to the organisation's configuration requirements. When a new 


## Overall structure

Iris uses a heirarchical model to manage objects.

### Group

Objects that belong to a group must not share any features with each other. 

### Class

Objects that belong to a class must:




Templates

Object templates can be instantiated. When a template is instantiated, the instance gets a copy of it's class':

* **facet schema**
* **field schema**, including any facet schemas attached to the field's class
* **hybrid lists**, including any facet schemas attached to the hybrid list's class
* **entitlements**, including any facet schemas attached to the entitlement's class
* **assets**, including any facet schemas attached to the asset's class



### Object Classes

Object are divided into classes, with each class representing a collection of things that are managed by Iris.


These objects have a number of common features:

* **Administrator-defined typing**: Each object can have one or more types, to which facet schemas may be assigned.




### Object types

The term *object* refers in general to any logical business data structure managed by Iris. Objects generally have a name or label to identify them, and belong to an `object type`.

### Extensible and non-extensible objects 

Objects may be `extensible` or `non-extensible`. An `extensible object` is one that that supports the creation of:

* Classes, and therefore,
* Facet schemas that are assigned to those classes.

`Extensible objects` may be thought of as the primary "things" that are managed by Iris.

`Non-extensible object` do not allow facet schema assignment. They may be thought of as things that exist to support `extensible objects`.

These object types are `extensible objects`:

* Entity
* Field
* Hybrid List
* Entitlement
* Asset

These object types `non-extensible objects` is below:

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
 
