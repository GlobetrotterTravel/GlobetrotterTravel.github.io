# Hybrid Lists

A `hybrid list` is a table of key/value pairs from which `entity` transactional data can be selectively assigned based on specific policies. `Hybrid lists` allow conformity of `item` selection across multiple `entities`, while remaining flexible for individual `entity` requirements.

The `items` in `hybrid lists` are shared across all `entities` that belong to the `entity` type to which the `hybrid list` is assigned. Depending on the inclusion policy, `entities` can extend or replace these `items` on an individual basis, ensuring that a globally shared set of `items` is available for selection without the added overhead of attempting to keep multiple `entities` in sync.

## In this section

* [Categories](#categories)
* [Items](#items)
* [Creating hybrid lists](#creating-hybrid-lists)
* [Enabling and disabling hybrid lists for specific entities](#enabling-and-disabling-hybrid-lists-for-specific-entities)
* [Entity inclusion policies](#entity-inclusion-policies)
## Categories

Each `hybrid list` has one or more `categories` to which a set of items belong. `Categories` allow for sets of `items` to be kept in the same `hybrid list` while being logically or semantically separated.

## Items

An `item` is a key/value pair that belongs to a `hybrid list` category. There are two types of `hybrid list` `items`:

* **Global items**: are placed on the `hybrid list` directly and are available to all `entities` of the typew to which the `hybrid list` is assigned.
* **Entity items**: are private `items` that are held by the entity. These items belong to the `hybrid list` but only for that `entity`.

This "hybrid" `item` selection is where this type of object gets its name. `Entities` can be assigned policies that determine which `items` are returned for a `hybrid list` under which circumstances.

## Creating hybrid lists

To create a `hybrid list`, the following information is needed:

* **Label**: The friendly label of the `hybrid list`. This must be unique across the instance.
* **Plural label**: The plural label of the `hybrid list`
* **Description**: What this `hybrid list` is for
* **Display order**: `Hybrid lists` are returned in ascending integer order

One or more categories are then created in the `hybrid list`, consisting of:

* **Label**: The friendly label of the `hybrid list` `category`, normally plural (as they contain many items). This must be unique inside the `hybrid list`.
* **Description**: What this `hybrid list` `category` is for
* **Display order**: `Hybrid lists` `categories` are returned in ascending integer order.

In each category, one or more `items` are created, consisting of:

* **Label**: The friendly label of the `item`. This must be unique inside the `hybrid list` `category`.
* **Value**: The value that will be returned when the `item` is selected.
* **Description**: What this `item` is for.

Items are displayed in label alphabetical order.

## Enabling and disabling hybrid lists for specific entities

For every `hybrid list` created, the relevant `entities` that are assigned that `hybrid list` receive a flag that either enables or disables their particular instance of the `hybrid list`. This flag is set as follows:

* **Enabled**: The entity will return this `hybrid list`
* **Disabled**: The entity will not return this `hybrid list`.

## Entity inclusion policies

Each `entity` has an inclusion policy for each defined `hybrid list` `category`. This policy controls whether global `items` are present when the `entity` defines their own `items` for a particular `hybrid list` `category`. The options are:

* **AddHead**: The `entity's` items are **added to the start** of the global items in the returned `entity's` `hybrid list`. Any `entity`-specificed `items` that have the same name as global `items` will replace the value on the global `item`.
* **AddTail**: The `entity's` items are **added to the end** of the global items in the returned `entity's` `hybrid list`. Any `entity`-specificed `items` that have the same name as global `items` will replace the value on the global `item`.
* **AddCombined**: The `entity's` items are **added and alphabetically sorted** into the global items of the `entity's` `hybrid list`. Any `entity`-specificed `items` that have the same name as global `items` will replace the value on the global `item`.
* **Replace**: The global `item` list is completely replaced by the `entity's` `item` list for that `category`.

The inclusion policy has no effect when there are no `entity`-specificed `items` for a particular category. The global `item` list is returned if the `hybrid list` is enabled for that `entity`. This policy also has no effect if the `entity's` `hybrid list` flag is set to `disabled`.

