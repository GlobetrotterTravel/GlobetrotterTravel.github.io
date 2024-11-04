# Entities

An `entity` represents a primary, independently managed unit within the system, such as a client, vendor, or partner, with which the organisation maintains a direct management relationship. `Entities` are the top-tier objects around which Iris is structured.Other components, such as `hybrid lists`, `entitlements`, and `field definitions` can be assigned to `entities` to build a complete configuration. `Entities` are foundational components that anchor all other managed relationships and configurations in the system.

`Entities` can be related to each other in parent-child relationship. For example a client `entity` definition could have a child entity definition of one or more people. `Entity` relationships are established at the tenant level, and then individual `entity` records are linked together according to these relationship rules.

## In this section

* [Default configuration](#default-configuration)
* [Creating entity definitions](#creating-entity-definitions)
* [Establishing relationships](#establishing-relationships)

## Classes and definitions

Like all components, `entities` are divided into `classes` which allow administrators to logically separate different `definitions`. After a `class` is established, one or more `entity definitions` may be created inside it. `Definitions` define the rules around how `entities` may relate to each other and what types of information they can hold.

Iris comes with two predefined `entity` definitions:

* Client
* Person

These `entity definitions` can be renamed or removed. In the default configuration, a Person is a child of Client, allowing records of those `definitions` to be linked together.

## Creating entity definitions

Further entity `definitions` can be created. To create an `entity definition`, supply the following information:

* **Label**: The label for the entity definition, identifying what definition of entity it is
* **Plural label**: The plural of the label, for example for "Person", it might be "People"
* **Description**: A description of what the entity definition represents and how it is intended to be used
* **Display order**: The order in which this entity definition should be displayed in an interface.

## Assigning packages

Once a `definition` is created, one or more `component packages` may be assigned. These `packages` provide a flexible data structure to fully describe an `entity`, its behaviour, and the definitions of data it holds.

## Establishing relationships

To establish a relationship between two `entities`, set the parent definition to another `entity` definition. This parent-child setup allows each `entity` to relate to others, forming flexible, multi-level hierarchies. Using this structure, you can create various relationship definitions, including:

* **Self-joins**: where an entity definition references itself, allowing, for example, a person to manage another person.
* **Circular relationships**: which can represent bidirectional or recursive relationships, such as a client referencing another client or a team lead who reports to a senior manager within the same entity definition.
* **Multiple child definitions**: allowing a single parent to have children of different definitions. For instance, a client entity might have both client and person child entities, enabling diverse relationships under one parent.

Iris manages recursion depth, ensuring that circular relationships do not enter infinite loops.

