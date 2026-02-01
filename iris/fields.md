---
layout: default
title: Fields
parent: Globetrotter Iris
nav_order: 40
has_children: true
has_toc: false
permalink: /iris/fields/
---

# Fields
{: .no_toc }

Fields are used to capture transactional information for each organization in the carrying out of their operational processes.

> 🛈 Fields are not used to store information about Iris resources - use facets for this purpose.

Iris maintains a customizable list of field definitions that are shared amongst all organizations. Although the name of each field definition is fixed when it is created, the label of each field instance can be customized per-organization, so that transactional reporting can be configured to each organization's requirements. If a field instance does not specify a customized label, then the field instance uses the default label from its parent field definition.

Because the collection of fields is controlled centrally, the MSP has tight control over the data flow produced by its processes. By allocating collection of transactional data to one or more fields for each client, the MSP can produce rich reporting and trend analysis to assist with continual improvement, compliance & audit and client value-add.

Iris does not collect transactional data directly. Instead, the combination of field definition and field instance information for each organization can be used by downstream systems to configure their data collection behaviour.

## Field Definitions

Each field definition has a unique name, label and a description of the intended purpose of this field. They also have a maximum length for values that will be inserted into the field instances derived from this definition. To assist with layout, fields also have a default order.

Most of these properties can be overridden on the derived field instance when it is created for an organization. Although the maximum length is specified on each field definition, this is informative only and intended for use in downstream systems when collecting data.

## Field Instances

Each field instance for an organization is derived from a single field definition. Organizations can have at most one field instance per definition, up to the maximum number of field definitions available.

The field instance is therefore specific to that particular organization, and customizations to the field instance are not shared to other organizations.

Field instances are flexible, allowing modification of the field's label, description, hint for user interfaces, display order, validation & requirement for data capture, as well as the items contained in the field instance and whether free text is allowed. Downstream systems can use these properties to configure deterministic behaviour on user forms and other data capture behaviours, whilst enforcing centralised data management practises through the use of field definition names.

## Names vs Labels

The name of each field instance is determined by the field definition's name from which it is derived. This name is fixed, and is always attached to the field instance. The field instance label is fully customizable per organization.

Because the name is always fixed, data capture is straightforward: the columns or fields in the database are always named the same, even if the labels expressed to the organization when presenting their data are customized. This combination allows the MSP to control data persistence while providing customized reporting capabilities to their clients.

## Dependencies

Field instances may be configured to support cascading dependencies between them. 

> 🛈 Dependencies on dynamically populated fields (e.g. set-bound fields) are not supported. When a dependency is set, the dependent field's items can be filtered based on the selected value in the upstream field.

For example, a set of project codes (items in one field instance) can be configured to be dependent on the item selection in a department field instance. The relationship is many-to-many. One or more items in field instance "A" may influence the ability to select one or more items in field instance "B".

Because field instances operate at the organizational level, this dependency can be configured per-organization, providing powerful customization for data entry forms that align to individual organizational requirements.

## Sets Behaviour

Field definitions can be bound to set definitions. When a field definition is bound to a set definition, any items in field instances derived from the field definition are suppressed in output. Instead, Iris will return the applicable organization-scoped records from the set as the field instance's items. Because sets run in realtime, any record collections that were modified (such as adding or removing organizations and people) are reflected straight away.

This powerful behaviour enables automatic sync between an organization or person resource and the field instance that those items feed. For example, an "authoriser" field definition can be bound with a collection of people who have the "Authoriser" facet set to "True". When the field instance derived from the "authoriser" field definition is called, it will run the set, capture the collection of authoriser-enabled people, and return them as items.

> 🛈 Field dependencies on fields that are populated by sets is not supported.

## In this section

- [Create a field definition](/iris/fields/create-a-field-definition/)
- [Pause or unpause a field definition](/iris/fields/pause-unpause-a-field-definition/)
- [Delete a field definition](/iris/fields/delete-a-field-definition/)
