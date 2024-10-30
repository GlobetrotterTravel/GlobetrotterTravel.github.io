# Fields

A `field` is a structure used to store values associated with transactional data for specific `entity` types. `Fields` are defined and managed as part of an `entity` type's configuration, allowing precise control over the types of data that each `entity` can record.

Each `field` is assigned to a specific `entity` type, with further configuration tailored to each `entity's` requirements.

`Fields` do not store data on the `entity` itself; rather, the `entity`-specific field configuration is returned to `subscriber` systems, enabling them to create forms that reflect that configuration. This allows `subscribers` to capture `entity`-specific values for `fields` during transactions, while organizations maintain a centralized `field` structure.

`Fields` are `metadata-aware` structures, meaning that field definitions can be assigned `dimension`, `attribute`, `property` and `tag` definitions. These facets can then be populated on an `entity`-specific basis. When an `entity` is retrieved, the `facet` values for the `entity's` `fields` are also returned, which allows administrators to define specific `field` behaviour in `subscriber` systems.

## Field types

All fields belong to one of the defined `field types`. Because facets are defined for a particular field type, all fields of that type inherit the appropriate facet configuration. This allows administrators to conform a set of fields to the same facet schema.



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

