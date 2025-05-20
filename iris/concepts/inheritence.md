---
layout: default
title: Inheritence
has_children: false
has_toc: false
permalink: /iris/concepts/inheritence/
---

# Inheritence
{: .no_toc }

Inheritence is a core-concept in Iris and affects the return behaviour of many data structures. Inheritance happens at the organization level, where organizations can inherit various resource types from their parent organizations. The following types of resources can be inherited:

* Entitlements
* Facets
* Fields
* Hybrid Lists
* Persons

The inheritence model is granular, allowing administrators to switch on and off the inheritence of various types of data structures. Each organization can set any combination of flags to either inherit or ignore the resources for each type at the organization's parent. This model allows global resources to be defined at higher organization levels while allowing specific organizations to override inheritance when they need customization.

When an organization has a parent, and that parent also has a parent, an inheritence chain is created. When a resource is requested for an organization, Iris keeps looking up the chain until it finds an organization that that satisfies either of two criteria:

* The organization does not itself have a parent, or
* The organization does not inherit the specified resource type.

This is referred to as an inheritence chain termination. Once Iris finds the terminating organization, the resource from that organization is returned. Since Iris does not allow circular parent/child relationships to be built, zero or more records are always returned.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Editing of inherited resources

Iris allows the editing and management of inherited resources, regardless of the presence of parent organizations or inheritence flags. Individual resource endpoints that target a specific resource by ID don't apply inheritance - they return exactly the requested resource, regardless of inheritance settings. This ensures administrators can directly manage any resource by its identifier. Aggregate endpoints (i.e. those that do not return a specified resource by id or other unique identifier) return effective views of resources by default, but this behaviour may be overridden with the route parameter `returnEffectiveView=false`.

In summary,

* GET operations on collections/aggregates respect inheritance by default (showing the "effective view")
* GET operations on specific resources by ID don't apply inheritance (showing the actual resource)
* POST/PUT/DELETE operations work on the actual resources directly

This approach ensures administrators have both a user-oriented view that respects inheritance (for most operations) as well as direct access to any specific resource for management.

## Inheritence and set behaviour

Set results obey inheritence semantics. When a set targeting `person` is run, only records that belong to chain terminating organizations are returned.

## Entitlements

An entitlement is something of value provided to an organization. Entitlement instances are derived from entitlement definitions, which contain a name and a description.

When an entitlement instance is created for an organization, a start and an end date is specified. This way, tenants can manage the validity of client entitlements by managing expiry periods and rebilling.

Only one entitlement instance can be assigned to each organisation per entitlement definition.
