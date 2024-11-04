# Glossary

### Jump To: [A](#admin) [C](#cancreate) [D](#definition) [E](#entitlement) [F](#facet) [H](#hybrid-list) [I](#inclusion-policy) [P](#package) [S](#subscriber) [T](#tag) [U](#user)

## admin

The default administrator user name and password for a newly stood-up instance of Iris.

## asset

A component that represents finite, assignable objects such as equipment, licenses, or digital resources. Assets are assigned to entities from a limited pool of instances, allowing each asset instance to be uniquely linked to a single entity at a time.

## asset definition

A structural template for an asset, specifying the properties and facets associated with the asset type. Asset definitions are used to create asset instances that can be assigned to entities.

## asset instance

An individual, finite item created from an asset definition, such as a piece of equipment or license. Asset instances are unique and can be assigned to a single entity at a time.

## asset package

A set of assets associated with an entity type, representing the assignable resources or items available for entity instances.

## attribute

A type of facet that records multiple independent string values. Attributes allow for arbitrary text entries associated with a component.

## canCreate

A permission that specifies that the records for this section may be created by the user.

## canDelete

A permission that specifies that the records for this section may be deleted by the user.

## canRead

A permission that specifies that the records for this section may be read by the user.

## canWrite

A permission that specifies that the records for this section may be modified by the user.

## category

A logical grouping within a hybrid list that holds multiple items. Categories help separate and organize items within a hybrid list for easier selection and assignment.

## class

A top-level grouping used to logically organize component definitions, such as entities, fields, or assets, for easier management and configuration.

## component

A core object that Iris manages, such as entities, fields, assets, or hybrid lists. Components relate to each other in structured ways to build a centralized view of an organization's configuration.

## component definition

A template that describes the structure, behavior, and metadata of a component, such as an entity or asset. Definitions provide a blueprint for creating instances.

## definition

A structural template for creating component instances, specifying the data fields, facet packages, and relationships that instances of the component will inherit.

## dimension

A type of facet that provides a globally shared list of choices, allowing only a single selection from this list. Each entity can be assigned only one value per dimension.

## entitlement

A component that establishes an entity's right to a particular service or benefit. Entitlements can be granted temporarily or permanently and are managed through entitlement definitions.

## entitlement definition

A template that specifies the conditions and privileges associated with an entitlement, such as access rights or service benefits. Entitlement definitions are used to create specific entitlements for entities.

## entitlement instance

An individual entitlement record created from an entitlement definition. Each entitlement instance grants a specific privilege or service access to an entity.

## entitlement package

A collection of entitlements assigned to an entity definition. The entitlement package outlines the available privileges or services that can be granted to entity instances.

## entity

A primary, independently managed unit within the system, such as a client, vendor, or partner. Entities serve as the main organizational units, around which other components are structured.

## entity definition

A template that defines the structure and allowed relationships for an entity. An entity definition includes facet and field packages, along with any entitlements or assets that can be assigned.

## entity instance

An individual record of an entity, created based on a specific entity definition. Instances hold actual data and are unique to each tenant.

## entity package

A collection of facets, fields, entitlements, and other components assigned to an entity definition to shape the entity's configuration and behavior.

## facet

A customizable metadata field assigned to components to store additional information. Facets are organized into packages and can be of different types, including dimension, attribute, property, and tag.

## facet definition

A template describing the type and structure of a facet, such as dimension, attribute, property, or tag. Facet definitions are used to establish the type of metadata that components can hold.

## facet instance

An individual metadata record created based on a facet definition. Facet instances hold specific data values for individual component instances, providing additional information such as tags, properties, or classifications.

## facet package

A collection of facets assigned to a component definition to define the additional data fields available for instances of that component. Facet packages enforce a structured layout for recording metadata.

## field

A component used to define specific data fields associated with transactional data for entities. Fields can be grouped into field packages and assigned to entity definitions to standardize data capture.

## field definition

A template that describes the configuration and data type of a field, such as text, number, or date. Field definitions are used to standardize data capture across entities.

## field instance

An individual data field created based on a field definition. Field instances are attached to entity instances and hold specific data values for those entities.

## field package

A collection of field definitions assigned to an entity definition, allowing specific fields to be recorded for instances of that entity. Each field in a package can be configured based on organizational needs.

## hybrid list

A structured list of categorized items that can contain both global and entity-specific items from which transactional data can be selectively chosen based on entity-specific policies.

## hybrid list definition

A template that outlines the categories and structure of a hybrid list. The hybrid list definition serves as the basis for creating hybrid list instances associated with specific entities.

## hybrid list instance

A specific, populated hybrid list created based on a hybrid list definition. Hybrid list instances contain categorized items that can be customized for each entity.

## hybrid list package

A set of hybrid lists associated with an entity type, used to standardize the items and categories available for data entry on those entities.

## inclusion policy

A policy applied to a hybrid list, specifying how items are selected or prioritized for an entity. Inclusion policies determine whether entity-specific items or global items appear in the list and in what order.

## instance

An individual record created based on a component definition. Instances are populated with unique data values while inheriting the structure from their respective definitions.

## item

A name/value pair within a hybrid list category, field or facet, representing a selectable option. Items can be global or entity-specific.

## package

1. **(non-facet)** A defined grouping of fields, entitlements, assets, or hybrid lists intended for assignment to a specified entity type.
2. **(facet)** A defined grouping of facets intended for assignment to component definitions such as entities, fields, entitlements, assets, or hybrid lists.

## permissions

A set of flags that determine a user's level of access to records within a section.

## property

1. **(system property)** A name/value pair that configures Iris' behavior at the tenant level.
2. **(facet property)** A single arbitrary value that can be recorded against a component to capture additional data.

## subscriber

A system that integrates with Iris via the REST API to consume and interact with data, such as retrieving entity configurations.

## tag

A type of facet that allows multiple selections from a globally shared choice list.

## tenantAdmin

A built-in role within Iris that provides full control of the tenant instance, allowing unrestricted access to all components and settings.

## type

A classification within a component definition, such as field types (e.g., Text, Number, Dropdown) or facet types (e.g., Dimension, Attribute, Property, Tag). Types define the behavior and structure of a component's data fields.

## user

An authorized individual who has access to Iris, based on specific roles and permissions.

