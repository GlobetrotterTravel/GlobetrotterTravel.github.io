# Glossary

### Jump To: [A](#admin) [C](#cancreate) [D](#dimension) [E](#entitlement) [F](#facet) [H](#hybrid-list) [I](#inclusion-policy) [P](#package) [S](#subscriber) [T](#tag) [U](#user)

## admin

The default administrator user name and password for a newly stood-up instance of Iris.

## asset

A component that can be assigned to an entity from a shared pool.

## attribute

A free text field that records multiple independent string entries.

## canCreate

A permission that specifies that the records for this section may be created by the user.

## canDelete

A permission that specifies that the records for this section may be deleted by the user. The permission `canRead` is also implied.

## canRead

A permission that specifies that the records for this section may be read by the user.

## canWrite

A permission that specifies that the records for this section may be modified by the user. The permission `canRead` is also implied.

## class

The top-level segmentation for component definitions.

## component

The core objects that Iris manages. Components relate to each other in specific ways to build a coherent centralised view of an orgainisation's service configuration.

## dimension

A globally shared list of choices from which a single selection can be made.

## entitlement

An `entitlement` establishes an `entity's` right to a particular service or benefit provided.

## entity

An `entity` represents a primary, independently managed unit within the system, such as a client, vendor, or partner, with which the organisation maintains a direct management relationship.

## entity definition

A `definition` of a `entity` that is assigned one or more `facet`, `field`, `entitlement`, `asset` and/or `hybrid list` packages.

## facet

A `dimension`, `attribute`, `property` or `tag`.

## facet package

A collection of `facets` that can be assigned to one or more `entity types` to further configure `entity instances` that use that `entity type`.

## field

A `component` that configures `subscriber` systems for capturing information on transactional data associated with an `entity`.

## field package

A collection of fields that are assigned to a specific `entity` type. The `field package` that appears in instances of that `entity type` can be further configured to suit the particular `entity instance`.

## hybrid list

A table of key/value pairs from which `entity` transactional data can be selectively assigned based on `entity`-specific policies.

## inclusion policy

A policy set on an `entity instance` that describes whether and how items on the `entity's` local `hybrid list` are selected instead of items on the globally shared `hybrid list`.

## item

A key/value pair that can be selected and recorded with transactional data.

## package

1. **(non-facet)** A defined grouping of `fields`, `entitlements`, `assets`, or `hybrid lists` intended for assignment to a specified `entity type`. 
2. **(facet)** A defined grouping of facets intended for assignment to `entity`, `field`, `entitlement`, `asset`, or `hybrid list` types.

## permissions

A set of flags that determine the degree to which a user may access a record.

## property

1. (system property): A key/value pair set on the tenant that modifies Iris' behaviour.
2. (related to facets): A free-text field that allows a single arbitrary value to be recorded.

## subscriber

A system that integrates with Iris via the REST API.

## tag

A globally shared list of values from which multiple selections may be made.

## tenantAdmin

A built-in role that provides full control of an Iris instance.

## user

A person that is authorised to use Iris.

