# Facets

Iris supports the addition of custom metadata on `components`. This metadata can record information in a way that matches the organisation's requirements, enabling a complete picture of the centralised configuration to be built. This metadata is recorded under one of four types: `dimension`, `attribute`, `property`, and `tag`. Collectively, these custom metadata objects are called `facets`.

An unlimited number of `facets` of different types can be created and assigned as a collection ("*`package`*") to any component.

When an `instance` of a `component` `definition` is created, further `instances` of all `facets` in the `facet package` are also created, which can record information about that `component's` `instance`, enabling a flexible data model to be built up while maintaining referential integrity and centralised management.

`Facets` support flags that assist with PII management and compliance such as determining the sensitivity and criticality levels of the data they hold.

# Facet packages

A collection of `facets` that are assigned to a particular `component definition` is called a *`facet package`*. Creating an `instance` of a `component` `definition` automatically creates `instances` of all `facets` in the `definition's` `facet package`. Although all `instances` for a particular `definition` use the same `facet package` (as it is assigned to the `definition`), the actual values assigned to each `facet instance` are different.

## How facets relate to entities

For each defined `entity` type, one or more `facets` can be defined. These facets work only with the specified `entity` type, enforcing a structure onto the data associated with an `entity`.

Each entity can then be loaded with the data specific to it, under the defined `facet` structure set up earlier. When an entity is returned, its associated facets and their values are also returned.

## Facet types

 `Facets` come in the following types:

* **Dimension**: `Dimensions` represent a single value that can be assigned to an entity from a globally shared list of choices. While the list of possible values for each dimension is shared among all `entities` to which the `dimension` is assigned, each `entity` is associated with only one of these values per `dimension` type at a time. 
* **Attributes**: `Attributes` can hold an unlimited number of arbitrary values.
* **Properties**: `Properties` hold a single arbitrary value.
* **Tags**: `Tags` hold multiple values from a single shared choice list.

`Facets` can be considered in terms of whether they support multiple values, and whether they allow arbitrary values. The following table is illustrative:

|               | Choice Values | Arbitrary Values |
|---------------|---------------|------------------|
| Single Value  | Dimension     | Property         |
| Multi Value   | Tags          | Attribute        |

