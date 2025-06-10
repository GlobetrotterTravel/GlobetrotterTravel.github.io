---
layout: default
title: Persons
parent: Resource Types
has_children: false
has_toc: false
permalink: /iris/resource-types/persons/
---

# Persons
{: .no_toc }

Persons are members of an organization.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Manager relationships

To build a logical representation of organization structure, each `Person` can have a manager (also a `Person`). When managers are assigned, a query can recurse up and down an organizational tree to determine the `Organization's` structure. Only a single manager can be assigned per Person, although a manager can have multiple persons reporting to them. Persons cannot be their own manager, and circular relationships are not allowed.

A manager `person` can be assigned to a `person` when the manager meets the following criteria:

The manager is active
The manager belongs to the same tenant as the person
The manager belongs to the same organization as the person.

If a manager is assigned to a `person` and is then deleted, the `person's` manager is set to `null`.