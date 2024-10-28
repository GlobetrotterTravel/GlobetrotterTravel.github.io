# Facets

Iris supports the addition of custom metadata on entities, which enables organisations to build a complete picture of their centralised configuration. This metadata is recorded under one of four types: `dimension`, `attribute`, `property`, and `tag`. Collectively, these custom metadata are called `facets`.

An unlimited number of `facets` of different types can be added to `entities`. These `facets` are returned when the `entity` is retrieved, so that each `entity` holds a complete record.

Flags can be set on `facet` types to determine the sensitivity and criticality levels of the data they hold.

## In this section

* [Facet types](#facet-types)
## Facet types

 `Facets` come in the following types:

* **Dimension**: `Dimensions` represent a single value that can be assigned to an entity from a globally shared list of choices. While the list of possible values for each dimension is shared among all `entities` to which the `dimension` is assigned, each `entity` is associated with only one of these values per `dimension` type at a time. 
* **Attributes**: `Attributes` can hold an unlimited number of arbitrary values.
* **Properties**: `Properties` hold a single arbitrary value.
* **Tags**: `Tags` hold multiple values from a single shared choice list.

As you can see from the list above, `Facets` can be considered in terms of whether they support multiple values, and whether they allow arbitrary values. The following table is illustrative:

|               | Choice Values | Arbitrary Values |
|---------------|---------------|------------------|
| Single Value  | Dimension     | Property         |
| Multi Value   | Tags          | Attribute        |
