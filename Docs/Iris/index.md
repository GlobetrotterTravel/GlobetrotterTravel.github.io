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

Within each component, administrators create <span title="The top-level segmentation for component definitions.">`classes`<span> and within `classes`, `definitions`. `Classes` serve to logically group different `component` `definitions` for management purposes, while `definitions` allow administrators to create a library of very specific component configurations that can be related to one another in a structured manner. It is these `definitions` that form the backbone of Iris' flexible structure.

### Instantiating entities

Instantiating an `entity` simply means creating a new record of that `entity's` particular `definition`. Because administrators create organisation-specific `definitions` for the above `components`, `components` can't be used directly. Instead, new `instances` of the `definitions` are created to hold the information needed.

Because `entity` is the primary component that Iris manages, it's the only component that can be directly instantiated, and this occurs when Iris receives a request for a new `entity` record along with an entity `definition` to use.

When an `entity's` `definition` is instantiated, the `instance` automatically gets further `instances` of the `definition's`:

* **facet package**
* **field package**, including any facet schemas attached to the field's type
* **hybrid list package**, including any facet schemas attached to the hybrid list's type
* **entitlements package**, including any facet schemas attached to the entitlement's type
* **assets package**, including any facet schemas attached to the asset's type

