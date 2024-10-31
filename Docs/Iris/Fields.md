# Fields

A `field` is a component used to store values associated with transactional data for specified `entity` types. Once defined, `fields` are bundled into `field packages` and assigned to an `entity` type. Individual entities can be configured to use or ignore these fields, allowing precise control over the types of data that each `entity` can record.

`Fields` do not store data on the `entity` itself; rather, the `entity`-specific field configuration is returned to `subscriber` systems, enabling them to create forms that reflect that configuration. This allows `subscribers` to capture `entity`-specific values for `fields` during transactions, while organizations maintain a centralized `field` structure.

`Fields` are `extensible components`, meaning that they can be assigned `facet packages`. These `facets` are then made available on the entitiescan then be populated on an `entity`-specific basis. When an `entity` is retrieved, the `facet` values for the `entity's` `fields` are also returned, which allows administrators to define specific `field` behaviour in `subscriber` systems.

## In this section

* [Field types](#field-types)
* [Creating fields](#creating-fields)
* [Facet definitions](#facet-definitions)
* [Field Assignment and Configuration](#field-assignment-and-configuration)
* [Runtime Integration](#runtime-integration)
* [Item source](#item-source)
    * [Static sources](#static-sources)
    * [Reference sources](#reference-sources)
    * [Reference source example](#reference-source-example)
    * [Hybrid list sources](#hybrid-list-sources)
    * [Hybrid list source example](#hybrid-list-source-example)
## Field types

All fields belong to one of the defined `field types`. Because facets are defined for a particular field type, all fields of that type inherit the appropriate facet configuration. This allows administrators to conform a set of fields to the same facet package.



## Creating fields

Fields are defined with the following properties:

* **Name**: The system name of the `field`. This name should not contain spaces or special characters and is intended for `subscriber` systems and reporting platforms to use as a column-name.
* **Label**: The default friendly name of the field. Subscribers should use this to display the field where an `entity`-specified name is not set.
* **Description**: The purpose of the `field`.

## Facet definitions

Fields can be assigned facet definitions. 

## Field Assignment and Configuration

Fields are assigned through a **Field Assignment** process, which links defined fields to specific entities. These assignments dictate which fields are available for a given entity instance, and each instance can be configured to use fields in a specific way, including display order and required status. This structure allows for flexible data entry configurations per entity type without modifying the underlying data structure.

## Runtime Integration

At runtime, a subscriber system accessing an entity record will receive the associated field configuration as part of that record. This configuration allows the subscriber to create data entry forms aligned with the entity’s field setup. Fields operate as configurable elements of the entity without altering the entity's core structure, providing a mechanism to capture additional data as defined by each entity’s needs.



## Item source

A field can have three item sources:

* Static
* Reference
* Hybrid list

### Static sources

Static items are kept in a list directly in Iris and returned with the entity's fields. Static items are useful for code lists that don't change very often.

### Reference sources

A field can be set to display its list of items from the existing records of any entity type that is a child of the entity type the field is placed on.

To set up a reference source, open the field and specify:

* **Entity type**: The entity type that holds the records this field should display
* **Label facet**: The facet to derive each item's name from
* **Value facet**: The facet to derive each item's value from

### Reference source example

For example, to set up a field called "Authoriser", carry out the following steps:

Create a new entity type under the "Person" class and call it "Authoriser".
Add a facet package to Authoriser entity type that records at least two values e.g. "Name" and "Email address". We will use these to populate the items.
Add a new field component in the appropriate Class, and call it "Authoriser".

In the field's configuration:
1. Set the "Field Control" to "Dropdown"
1. Set the "Item source" to "Reference".

In the Reference configuration,
1. set the "Entity type" to "Authoriser"
1. Iris finds the facets that are assigned to the entity type
1. Set the Label Facet to "Name". This is the label that is intended to be shown on a user interface
1. Set the Value Facet to "Email address". This is the value that is intended to be dropped into the transactional data.

| i The Label and Value can be the same facet.

Retrieve the client entity. In the Fields array, The Authoriser field will be populated with a list of active records from the Client Entity's Authoriser child.

In this configuration, when records are added or removed from the Authoriser entity type, the field items are automatically updated as well.

### Hybrid list sources

In this configuration, items for the field are taken from the nominated hybrid list. The hybrid list must be assigned to the entity the field is set up on.

To set up a hybrid list source, open the field and specify:

* **Hybrid list name**: The hybrid list that holds the items you want to produce in the field
* **Category**: The hybrid list category that the items belong to.

### Hybrid list source example

For example, to set up a field called "Air exception code", carry out the following steps:

1. Create a new hybrid list called **Exception Codes**.
1. Create three categories for "Air", "Car" and "Hotel".
1. Under "Air", create a number of exception code items, specifying the label and value for each one.
1. Save the hybrid list

Add a new field component in the appropriate Class, and call it "Authoriser".

In the field's configuration:
1. Set the "Field Control" to "Dropdown"
1. Set the "Item source" to "Hybrid List".
1. Choose the category "Air".

Optionally, create a hybrid list instance for the entity, adding a few entity-specific exception codes. Set the hybrid list's `inclusion policy` appropriately.

Retrieve the client entity. In the Fields array, the **Air exception code** field will have items from either the standard **Exception Codes** hybrid list, the entity's **Exception Codes** list or a combination of the two, depending on the entity's `inclusion policy` for that hybrid list.
