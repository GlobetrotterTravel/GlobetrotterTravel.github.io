---
layout: default
title: Sets
parent: Resource Management
has_children: false
has_toc: false
permalink: /iris/resource-management/sets/
---

# Sets

A `set` is a predefined group of filters that operates on either the `organization` or `person` lists. When run, sets look in the target items for facet values that match the filters, and return any organizations or persons that have matching facets. Because the search is run every time the set is called, new and modified items that match or do not match are included or removed from results dynamically, providing flexibile management of these items according to their common facet values.

A set definition can be used as the source for a field. When a field has a set definition assigned, the items on any field instances for that definition are ignored, and the set is run each time the field instance is called. Only records that belong to the field instance's organization or child organizations are returned.

For example, a set could be created for persons, with a filter on job title = "Manager". The manager's set can be placed on a field definition called "Approver". When an instance of the Approver field definition is returned, the set is run and all person belonging to the field instance's organization (and their child organizations) are returned instead of static items.

## Sets as field item sources

When used as a field source, the following mapping is applied:

For target type organization:

| The Field Item's | Is Mapped From |
|---|---|
| Name | Number - Name |
| Value | Number |
| Description | Number - Name |

For target type person:

| The Field Item's | Is Mapped From |
|---|---|
| Name | First Name - Last Name |
| Value | Email Address |
| Description | First Name - Last Name |