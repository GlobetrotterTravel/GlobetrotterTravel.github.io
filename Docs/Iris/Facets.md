# Facets

Iris supports the addition of custom metadata on `extensible objects`. This metadata can record information in a way that matches the organisation's requirements, enabling a complete picture of the centralised configuration to be built. This metadata is recorded under one of four types: `dimension`, `attribute`, `property`, and `tag`. Collectively, these custom metadata objects are called `facets`.

An unlimited number of `facets` of different types can be created and assigned as a collection ("*`package`*") to any extensible object class.

When an instance of an extensible class object is created, the facet package can record information about that object, enabling 


Flags can be set on `facet` types to determine the sensitivity and criticality levels of the data they hold.

## In this section

* [Facet types](#facet-types)

## Supported classes

The following object `types` are extensible and therefore support `facet package` assignment:

* Entity
* Field
* Hybrid List
* Entitlement

# Facet packages

A collection of `facets` that are assigned to a particular `class` of object is called a *`package`*. Creating an object of a `class` automatically makes available the entire `facet` package that is assigned to that `class`. Although the package is the same for all objects in a class, the actual values assigned to the object instances are different.



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

