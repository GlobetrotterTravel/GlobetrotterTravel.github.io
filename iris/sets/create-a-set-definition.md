---
layout: default
title: Create a set definition
parent: Sets
grand_parent: Globetrotter Iris
nav_order: 10
permalink: /iris/sets/create-a-set-definition/
---

# Create a set definition
{: .no_toc }

Sets save a predefined filter over a collection of organization or person records.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Create a new set definition

To create a new `set definition` open the `Definitions` navigation menu and click `Set Definitions`, then click `+ Create Set Definition`.

The `Create New Set Definition` panel appears.

### Details tab

Populate details.

1. `Name`: the friendly name of the set as will be displayed on forms. It can contain spaces, mixed casing etc.
2. `Description`: a brief description of the purpose of this set
3. `Target Resource`: the resource (either organization or person) that will be returned by this set.

> 🛈 Sets can apply to either organization or person, but not both.

### Filters tab

Click `+ Add Filter`. The `Add Filter` dialog appears.

1. `Facet Definition`: Select the facet that is associated with the record type that you want to create a filter on
2. `Filter Operator`: Select the operator to be applied to the facet. Currently, Iris supports the `Equals` operator.
3. `Value`: Type the string value that the set should match.

> 🛈 When saved, the set will return all records of `Target Resource` where the `Value` aligns with the `Filter Operator` for the selected `Facet Definition`. For example, a set of organizations where the Country facet is equal to "Singapore".

Add further filters as required.

Click `Save`.
