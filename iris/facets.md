---
layout: default
title: Facets
parent: Globetrotter Iris
nav_order: 30
has_children: true
has_toc: false
permalink: /iris/facets/
---

# Facets
{: .no_toc }

The facet system provides a powerful framework for managing data for organizations, people, service accounts, fields, hybrid lists and entitlements. There are four facet types: dimensions, attributes, properties and tags. Each facet type stores a different type of information, according to whether they a) have a defined list of allowed values to select from, or they are free-text, and b) whether or not they allow multiple values to be entered.

| Facet Type | Select From List | Allows Multiple Values | Example |
|------------|------------------------|------------------------|---------|
| Dimensions | Yes | No | Client Relationship Manager |
| Attributes | No | Yes | Client's domain names |
| Properties | No | No | Job title |
| Tags | Yes | Yes | Client user roles |

A facet definition can be created that targets either an organization or a person. Creating multiple different types of facets for resources allows a logical and flexible data model for these resources to be built up.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Dimensions

Dimensions represent globally shared lists of information, from which a single value can be assigned to an entity or field. While the list of possible values for each dimension is shared, each entity or field is associated with only one of these values at a time. Dimensions are therefore useful for pivoting data around a particular value.

For example, the Client Relationship Manager dimension would have a list of all the client relationship managers in the company. When a client entity is created, the user can select the client relationship manager from the list of available values.

Dimensions should be used when the list of possible values is known in advance and only one value should be assigned at a time. In this example the list of client relationship managers is known and shared among all clients, and only one client relationship manager can be assigned to a client at a time.

## Attributes

Attributes capture free-text information about an entity or field, where multiple values can be entered. Unlike dimensions, attributes do not require a predefined list of values, offering flexibility in data entry. This facet type is ideal for capturing diverse or varied information that doesn't conform to a standard set of options.

For instance, in the "Client Domain Names" attribute, a client entity could have multiple domain names associated with it. Users can freely add domain names as needed, providing the ability to capture all relevant information without being restricted to a pre-set list.

Attributes are best utilized when data cannot be confined to a predetermined list and there is a need to associate multiple values with a single entity or field. In the domain names example, the varied nature of domain names and the possibility of a client owning multiple domains necessitate a flexible, multi-entry approach.

## Properties

Properties are designed for capturing specific, singular free-text information about an entity or field. This facet type does not allow for multiple values and does not rely on a predefined list, making it suitable for distinct, individual data entries.

An example of a property could be the a client user's "Job Title". Each user entity would have a specific job title associated with it, entered as free text.

Properties are most effective when the information is unique to each entity or field and when only one piece of information is necessary. In the job title scenario, each user has one distinct title, but the list of possible titles for all users is not knowable, making property the ideal facet type for this data.

## Tags

Tags are utilized for categorizing entities or fields with one or more values from a predefined list. This facet type combines the flexibility of selecting multiple values with the structure of a predefined list, ideal for organizing data into distinct, recognizable categories.

For example, in the "Client user roles" tag, each client user entity can be associated with multiple roles from a set list. Iris users can select all applicable roles for a client user, allowing the user to be categorised in multiple ways.

Tags are optimal when there is a need to categorize entities or fields under multiple predefined labels. The ability to select multiple values from a list, as seen in the client user roles example, makes tags a versatile tool for detailed and flexible categorization.

## Data classification for facets

Each facet is given a classification to indicate the sensitivity of the data it stores. The classifications are:

* Public
* Internal
* Confidential
* PII

Public facets store information that is already freely available or disclosed by either the client or the service provider. Since this information is non-PII, it can be accessed and shared with minimal restrictions. Examples include marketing materials, publicly available contact information, general product descriptions and the general configuration of a client's services.

Internal facets store information that is not normally publicly disclosed, but would be not subject to restrictions within normal operating environments. Information such as configurations for operating procedures, server names, approval lists, lookup lists etc. where disclosure of the information by itself would not present great risk.

Confidential facets store information that is not publicly available and is not disclosed by either the client or the service provider. Information that, if unauthorized access or disclosure occurs, could result in financial or reputational harm to the organization or its clients. These facets are restricted to users that belong to the service provider. Examples include client-specific service configurations, internal policies, business strategies, non-public financial information, client data not classified as PII but still PII.

PII facets store highly PII information including Personally Identifiable Information (PII) or data whose unauthorized access could lead to serious legal, financial, reputational, or commercial consequences. These facets are restricted to users that belong to the service provider, and require a higher role to access. Examples include passport details and other government identifiers, credit card information, detailed personal profiles, legal documents, critical business secrets. Facets of type `Secret` are always classified as PII.

PII refers any information relating to an identified or identifiable natural person. An identifiable natural person is one who can be identified, directly or indirectly, with any of the following:

* Name
* Identification number
* Location coordinates
* Online identifier
* Other specific factors
* Physical
* Physiological
* Genetic
* Mental (psychological)
* Economic
* Cultural
* Social identity


## In this section

- [Create a facet definition](/iris/facets/create-a-facet-definition/)
- [Pause or unpause a facet definition](/iris/facets/pause-unpause-a-facet-definition/)
- [Delete a facet definition](/iris/facets/delete-a-facet-definition/)
