---
layout: default
title: Create an entitlement definition
parent: Entitlements
grand_parent: Globetrotter Iris
nav_order: 10
permalink: /iris/entitlements/create-an-entitlement-definition/
---

# Create an entitlement definition
{: .no_toc }

Entitlement definitions define the different services that an MSP offers to its clients. Entitlement definitions hold facets that entitlement instances can set per-organization to configure the particulars of those entitlements for each organization.

## Create an entitlement definition

To create a new entitlement definition, open the `Definitions` navigation menu and click `Entitlement Definitions`, then click `+ Create Entitlement Definition`.

The `Create New Entitlement Definition` panel appears.

Populate the form:

* **Category**: Enter a logical convenience grouping for the entitlement definition. You may want to categorise by department, service line or tier.
* **Name**: Enter the name of the entitlement. It must be unique across entitlement definitions.
* **Description**: Describe what benefits this entitlement grants to the client.
* **Data Sensitivity**: The particulars of some entitlements can be sensitive. If facets associate with the entitlement might contain sensitive information (e.g. server locations) then set the `Data Sensitivity` appropriately.
* **Default Term**: Enter the default term in days for each entitlement instance that will be created from this definition.
* **Allow Multiple Instances**: If a single organization can have multiple instances of this definition, check this box. If unchecked, only a single instance of this definition can be assigned to each organization.
* **Active**: Controls whether this definition can be used when creating instances.

Click `Save`. The entitlement definition is created.

To configure facets for the entitlement definition, see the section on [creating a facet definition](/iris/facets/create-a-facet-definition/).