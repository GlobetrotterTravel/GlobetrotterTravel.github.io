---
layout: default
title: Create a facet definition
parent: Facets
grand_parent: Globetrotter Iris
nav_order: 10
permalink: /iris/facets/create-a-facet-definition/
---

# Create a facet definition
{: .no_toc }

Facet definitions control the data that can be captured about a resource. All resources support facet definitions.

Organizations, Persons and Service accounts do not have subtypes, and therefore facet definitions apply to them globally.

Field Definitions, Hybrid Lists and Entitlements have subtypes. Each facet definition refers to a specific subtype for these resources, enabling tight control over the information stored on their instances.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Create a new facet definition

To create a facet definition, open the `Definitions` navigation menu and click `Facet Definitions`, then click `+ Create Facet Definition`

The `Create New Facet Definition` panel appears.

### Details tab

Populate details.

1. `Name`: the friendly name of the facet as will be displayed on forms. It can contain spaces, mixed casing etc.
2. `Category`: controls the tab that the facet appears in in the resource's editing panel
3. `Display order`: set an integer value that controls the order in which the facet will appear under the `category` tab. Lower numbers are displayed first, and it's recommended that orders are initially set in blocks of ten: 10, 20, 30 etc. to allow room for interpolation.
4. `Description`: a brief description of the purpose of this facet
5. `Target Resource`: the resource that will hold this `facet definition`. If you select a resource other than `Organization`, `Person` or `Service Account`, also select the resource's definition that the facet definition will belong to.

> 🛈 Each resource definition can hold its own facets, allowing flexibility between resources of the same type. For example, an entitlement definition "A" can have different facets than entitlement definition "B".

6. `Data Sensitivity`: the classification of the data that this `facet definition` holds. The levels are:

- `PII`: Personally Identifiable Information (PII) that requires the highest level of protection
- `Confidential`: information that is commercially sensitiv
- `Internal`: information that pertains to internal operations.
- `Public`: unrestricted information that can be shared publicly without any concerns.

7. `Visibility Scope`: which level of role the `facet definition` should be visible to.  The levels are:

- `Self`: All users who have access to Iris will see this `facet definition`. In practice, this only applies to facet definitions that belong to Person resources, since more restrictive visibility scopes will not appear to users that do not have at least organizational level roles.
- `Organization`: Users who have access to their own organization(s).
- `Hierarchy`: Users who have access to their organization(s) and the organization's children.
- `Tenant`: Users who have access to all organizations and persons within a tenant (i.e. users that belong to the MSP).

A full discussion of access levels is found in `Roles, Permissions and Access Levels`.

### Settings tab

Select options for the `facet definition`.

1. `Critical`: select this if the lack of a value for this `facet definition` should be considered problematic.
2. `Allow Free Text`: select this if the `facet definition` should allow free text entry when setting the value.
3. `Allow Multiple Values`: select this value if the `facet definition` should allow multiple values to be set.

### Values tab

Add any predefined values that should be presented when populating the facet.

For each value to add, enter the value and click `+ Add`. To remove a value, click the `garbage can` icon next to the value.
