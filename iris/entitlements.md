---
layout: default
title: Entitlements
parent: Globetrotter Iris
nav_order: 55
has_children: true
has_toc: false
permalink: /iris/entitlements/
---

# Entitlements
{: .no_toc }

Entitlements represent the services and benefits that an MSP offers to its clients. By defining entitlements centrally and assigning instances to organizations, MSPs can track service delivery, manage service levels, and automatically configure downstream systems based on each client's entitlements.

Entitlement definitions describe the available services, while entitlement instances represent the specific assignment of those services to individual organizations.

## Entitlement definitions

Entitlement definitions describe the services that can be offered to clients. They act as templates that specify what each service entails, how long it typically lasts, and what configuration options are available. Definitions can be organised into categories to reflect your service catalogue structure, for example by department, service line, or tier.

Some services may need to be assigned multiple times to the same organization-for example, separate support contracts for different business units, or recurring subscriptions that renew independently. Entitlement definitions can be configured to allow this flexibility where needed.

Entitlement definitions can also have facets attached to capture configuration details specific to each service offering. This allows you to define structured data fields that will be populated when instances are assigned to organizations.

## Entitlement instances

Entitlement instances are assigned to organizations to record which services each client has access to. Each instance represents an active subscription or service agreement, with defined start and end dates that track the service term. Perpetual entitlements can be created by leaving the end date open.

Instances inherit the facets from their parent definition, allowing you to configure service-specific details for each organization. This enables downstream systems to automatically apply the correct service levels based on centrally managed entitlement data.

> 🛈 Organizations can have at most one instance per entitlement definition, unless the definition allows multiple instances.

## Facets

Entitlements can have facet instances assigned to capture configuration details beyond the core properties. Facets allow you to store structured data such as service parameters, configuration options, or any other entitlement-specific attributes defined by your facet definitions. These facets can configure downstream systems to automatically apply the correct per-organization configuration.

## In this section

- [Create an entitlement definition](/iris/entitlements/create-an-entitlement-definition/)
- [Delete an entitlement definition](/iris/entitlements/delete-an-entitlement-definition/)
- [Pause or unpause an entitlement definition](/iris/entitlements/pause-unpause-an-entitlement-definition/)