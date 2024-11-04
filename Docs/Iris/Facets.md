# Facets

Iris supports the addition of custom metadata on `components`. This metadata can record information in a way that matches the organisation's requirements, enabling a complete picture of the centralised configuration to be built. This metadata is recorded under one of four types: `dimension`, `attribute`, `property`, and `tag`. Collectively, these custom metadata objects are called `facets`.

An unlimited number of `facets` of different types can be created and assigned as a collection ("*`package`*") to any component.

When an `instance` of a `component` `definition` is created, further `instances` of all `facets` in the `facet package` are also created, which can record information about that `component's` `instance`, enabling a flexible data model to be built up while maintaining referential integrity and centralised management.

`Facets` support flags that assist with PII management and compliance such as determining the sensitivity and criticality levels of the data they hold.

## In this section

* [Facet packages](#facet-packages)
* [Assigning facet packages](#assigning-facet-packages)
* [How facets relate to entities](#how-facets-relate-to-entities)
* [Facet types](#facet-types)
* [Creating a dimension](#creating-a-dimension)
* [Creating an attribute](#creating-an-attribute)
* [Creating a property](#creating-a-property)
* [Creating a tag](#creating-a-tag)
* [Critical flag](#critical-flag)
* [Entity visible flag](#entity-visible-flag)
* [Data classifications](#data-classifications)


## Facet packages

A collection of `facets` that are assigned to a particular `component definition` is called a *`facet package`*. Creating an `instance` of a `component` `definition` automatically creates `instances` of all `facets` in the `definition's` `facet package`. Although all `instances` for a particular `definition` use the same `facet package` (as it is assigned to the `definition`), the actual values assigned to each `facet instance` are different. `Facet packages` therefore enforce a structure onto the data associated with a `component`.

## Assigning facet packages

Facet packages can be assigned to the following components:

* Entity
* Field
* Hybrid List
* Entitlement
* Asset

`Facet definitions` themselves cannot be assigned `facet packages`.

## How facets relate to entities

For each defined `entity definition`, one or more `facets` can be defined. These facets work only with the specified `entity` type, 

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

## Creating a dimension

To create a dimension, supply the following information:

* **Name**: The unique name of the dimension
* **Description**: Describe this dimension's purpose
* **Items**: A list of items that may be selected for the dimension.

## Creating an attribute

To create a attribute, supply the following information:

* **Name**: The unique name of the attribute
* **Description**: Describe this attribute's purpose

## Creating a property

To create a property, supply the following information:

* **Name**: The unique name of the property
* **Description**: Describe this property's purpose

## Creating a tag

To create a tag, supply the following information:

* **Name**: The unique name of the tag
* **Description**: Describe this tag's purpose
* **Items**: A list of items that may be selected for the tag.

## Critical flag

`Facet definitions` may be set as `critical`. A `critical facet` is one that performs an important role in a business process and therefore should be populated. Subscribers can check `critical facets` for appropriate values and halt processing if required. Iris will warn if a record is saved with unpopulated values in `critical facets`.

## Entity visible flag

Organisations may want to hold internal information about entities that should not be shared with the entity. Because Iris can allow entities to access their own records, `facet definitions` can be set to hide this organisation-only information. `Instances` of `facet definitions` that have the **Entity visible** flag set to false are not returned to `entities` when accessing their own records. 

## Data classifications

`Facet definitions` support the setting of a `data classification`. The classification is administrator defined. Out of the box, Iris comes with the following classifications:

1. **Public**: Data with no confidentiality requirements that can be shared with the public without any impact.
2. **Internal Use Only**: Non-public information shared only with employees or authorised parties. Unauthorized disclosure could have a minor impact.
3. **Confidential**: Information that must be restricted due to potential legal, operational, or reputational damage if disclosed to unauthorized parties.
4. **Highly Confidential**: Highly sensitive information, with access strictly limited to specific individuals. Unauthorized access could lead to severe damage.

These classifications can be aded to, modified or deleted as needed. When `facet instances` are returned attached to components, the classification for the `facet` is also returned, allowing subscriber systems to make decisions around divulging the `facet` value.

| 🛈 The `facet definition's` `Data classification` and **Entity visible** flag do not have any effect on each other. It is possible for a `facet definition` to be classified as **Highly Confidential** and **Entity visible**, providing flexibility for cross-business processes.
