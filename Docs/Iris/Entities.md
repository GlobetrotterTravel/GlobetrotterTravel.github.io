# Entities

An `entity` represents a primary, independently managed unit within the system, such as a client, vendor, or partner, with which the organisation maintains a direct management relationship. Entities are the top-tier objects around which Iris is structured. They can own or be associated with multiple configuration objects, such as `hybrid lists`, `entitlements`, and `field definitions`. `Entities` are foundational components that anchor all other managed relationships and configurations in the system.

`Entities` can be related to each other in parent-child relationship. For example a client `entity` could have a child entity of one or more people. `Entity` relationships are established at the tenant level, and then individual `entity` records are linked together according to these relationship rules.

## Default configuration

Iris comes with two predefined `entity` types:

* Client
* Person

These `entity` types can be renamed or removed. In the default configuration, a User is a child of Client, allowing records of those types to be linked together.

## Creating entity types

Further entity types can be created. To create an entity type, supply the following information:

* **Label**: The label for the entity type, identifying what type of entity it is
* **Plural label**: The plural of the label, for example for "Person", it might be "People"
* **Description**: A description of what the entity type represents and how it is intended to be used
* **Display order**: The order in which this entity type should be displayed in an interface.

## Establishing relationships

To establish a relationship between two `entities`, set the parent type to another `entity` type. This parent-child setup allows each `entity` to relate to others, forming flexible, multi-level hierarchies. Using this structure, you can create various relationship types, including:

* **Self-joins**: where an entity type references itself, allowing, for example, a person to manage another person.
* **Circular relationships**: which can represent bidirectional or recursive relationships, such as a client referencing another client or a team lead who reports to a senior manager within the same entity type.
* **Multiple child types**: allowing a single parent to have children of different types. For instance, a client entity might have both client and person child entities, enabling diverse relationships under one parent.

Iris manages recursion depth, ensuring that circular relationships do not enter infinite loops.

