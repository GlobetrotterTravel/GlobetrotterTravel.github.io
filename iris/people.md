---
layout: default
title: People
parent: Globetrotter Iris
nav_order: 20
has_children: true
has_toc: false
permalink: /iris/people/
---

# People
{: .no_toc }

People represent the individuals associated with organizations in your ecosystem. By maintaining detailed records of people and their relationships to organizations, MSPs can manage contacts, control access, and capture person-specific data across their client base.

In Iris, people are linked to one or more organizations and can have facet instances assigned to capture additional information. People with login credentials can also be assigned roles that control their access to Iris functionality.

## Identification

Each person is identified by the following properties:

- **First Name**: An optional first name for the person.
- **Last Name**: The person's surname.
- **Email Address**: A unique email address that identifies the person across all organizations.
- **User Principal Name (UPN)**: An optional identifier that enables direct login to Iris, typically matching the email address.

> 🛈 The email address must be unique across all people in the system.

## Organization membership

People must belong to at least one organization, but can be members of multiple organizations simultaneously. This flexibility allows you to model scenarios where individuals have relationships with several entities—for example, a consultant who works with multiple clients, or an employee who belongs to both a parent company and a subsidiary.

Organization membership determines which people appear in organization-scoped views and influences the results of person-based sets.

> 🛈 Each person must be assigned to at least one organization.

## Roles and access

When a person has a User Principal Name (UPN) assigned, they can log in to Iris directly and be assigned roles that control their access to system functionality.

Roles are assigned after the person record is saved and the UPN is configured. Each role grants specific permissions and can be scoped to particular areas of the system.

> 🛈 Do not assign a UPN or roles until you have read and understood the section on roles and security.

## Facets

People can have facet instances assigned to capture additional information beyond the core identification fields. Facets allow you to store structured data such as job titles, department affiliations, contact preferences, or any other person-specific attributes defined by your facet definitions.

See the section on facets for more information.

## In this section

- [Create a person](/iris/people/create-a-person/): How to create a new person
- [Delete a person](/iris/people/delete-a-person/): How to delete a person
