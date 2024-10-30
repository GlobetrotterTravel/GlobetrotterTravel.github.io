# Fields

A `field` is a structure used to store values associated with transactional data for specific `entity` types. `Fields` are defined and managed as part of an `entity` type's configuration, allowing precise control over the types of data that each `entity` can record.

Each `field` is assigned to a specific `entity` type, with further configuration tailored to each `entity's` requirements.

`Fields` do not store data on the `entity` itself; rather, the `entity`-specific field configuration is returned to `subscriber` systems, enabling them to create forms that reflect that configuration. This allows `subscribers` to capture `entity`-specific values for `fields` during transactions, while organizations maintain a centralized `field` structure.

`Fields` are `metadata-aware` structures, meaning that field definitions can be assigned `dimension`, `attribute`, `property` and `tag` definitions. These facets can then be populated on an `entity`-specific basis. When an `entity` is retrieved, the `facet` values for the `entity`'s `fields` are also returned, which allows administrators to define specific `field` behaviour in `subscriber` systems.

## Creating fields




## Field Assignment and Configuration

Fields are assigned through a **Field Assignment** process, which links defined fields to specific entities. These assignments dictate which fields are available for a given entity instance, and each instance can be configured to use fields in a specific way, including display order and required status. This structure allows for flexible data entry configurations per entity type without modifying the underlying data structure.

## Runtime Integration

At runtime, a subscriber system accessing an entity record will receive the associated field configuration as part of that record. This configuration allows the subscriber to create data entry forms aligned with the entity’s field setup. Fields operate as configurable elements of the entity without altering the entity's core structure, providing a mechanism to capture additional data as defined by each entity’s needs.

--- 

This should keep the focus tight and descriptive. Let me know if further clarity is needed on any section.