---
layout: default
title: Security Model
has_children: false
has_toc: false
permalink: /iris/concepts/security-model/
---

# Security Model

## Overview

The Iris system employs a hierarchical organization-based security model that controls access to resources throughout the application. This document describes how the security model functions and provides guidance for system administrators responsible for managing user access within the Iris platform.

## Authentication Methods

Iris supports two authentication methods:

1. **User Accounts** - Human users authenticate using JWT bearer tokens. Users provide their credentials (username and password) to obtain a token that is subsequently used for API requests.

2. **Service Accounts** - Automated systems authenticate using client certificates (mTLS). Each service account is identified by a unique certificate fingerprint that is registered within the system.

Both authentication methods integrate with the same underlying authorization system, allowing consistent access control regardless of the authentication method employed.

## Multi-Tenant Architecture

Iris is designed as a multi-tenant application where tenant isolation is enforced at all levels of the system. Tenant identification occurs automatically upon authentication, and all operations within the system are restricted to the authenticated user's tenant. Cross-tenant operations are prohibited, ensuring complete data isolation between tenants.

## Organization Hierarchy

At the core of the Iris security model is the concept of organization hierarchy. Organizations within a tenant can be structured in a parent-child relationship, forming a tree structure. This hierarchy is central to determining access permissions throughout the system.

When a user or service account is created, it is associated with a specific organization. This organizational association determines the scope of resources the account can access. The hierarchy-based rules are as follows:

- An account associated with `InvalidOrganization` (empty GUID) has no access to any organizational resources.
- An account associated with `AllOrganizations` (GUID with all bits set) has access to all organizations within its tenant.
- An account associated with a specific organization has access to:
  - The organization itself
  - All child organizations (direct descendants)
  - All indirect descendants (children of children) at any depth in the hierarchy

This hierarchical access model allows for flexible access control that naturally mirrors the organizational structure of most enterprises.

## Role-Based Permissions

Within the organization-based boundary, more granular access control is achieved through a role-based permission system. Each user or service account is assigned one or more roles, and each role contains a set of permissions.

Permissions in Iris follow a resource-action pattern, where a permission represents the ability to perform a specific action on a particular resource type. For example:

- `organizations:read` - Permission to view organization information
- `persons:create` - Permission to create new person records
- `reports:export` - Permission to export reports

When an account attempts to perform an operation, the system checks:

1. If the account has organization-level access to the resource
2. If the account has the required permission to perform the requested action

Both conditions must be satisfied for the operation to proceed.

## Special Organization Types

The system recognizes two special organization identifiers:

## All Organizations Access

Accounts assigned to the `AllOrganizations` identifier are granted tenant-wide access to all organizations within their tenant. This special designation is typically reserved for administrative accounts that need to manage all organizations.

System administrators should be cautious when assigning accounts to `AllOrganizations`, as this grants broad access across the entire tenant. This access level is appropriate for:

- Tenant administrators responsible for managing the entire tenant
- Reporting services that need to generate cross-organizational analytics
- Administrative tools that manage global configuration settings

## Invalid Organization Access

Accounts assigned to the `InvalidOrganization` identifier are denied access to all organization-specific resources. This configuration is useful for accounts that should only access non-organizational resources or as a temporary state when suspending account access.

## Application to Resources

Resources in Iris fall into two categories with respect to security:

## Organization-Owned Resources

Most resources in the system are associated with a specific organization. These resources are automatically filtered based on the authenticated account's organization access. An account can only access resources that belong to organizations within its access scope.

## Global Resources

Some resources are not directly tied to a specific organization but may still have visibility rules that respect the organization hierarchy. These resources are secured through explicit permission checks rather than automatic organization filtering.

## Practical Examples

## Example 1: Regional Management Structure

A company has a head office with regional offices beneath it. Each regional office manages multiple branch offices.

- An account associated with the head office organization has access to all regional and branch offices.
- An account associated with a regional office has access to that region and all branch offices within that region, but not to other regions.
- An account associated with a branch office has access only to that specific branch office.

## Example 2: Cross-Organizational Reporting

A reporting service needs to analyze data across multiple organizations. This service account would be configured with:

1. Association to the `AllOrganizations` special identifier
2. Assignment of roles with appropriate read permissions for the necessary resources
3. Authentication via a client certificate

The service would then have read access to resources across all organizations within its tenant, enabling comprehensive reporting capabilities.

## Security Considerations

The organization-based security model in Iris is designed to be robust and secure, but system administrators should be aware of several important considerations:

## Organization Structure Changes

When modifying the organization hierarchy, carefully consider the security implications. Moving an organization to a new parent affects which accounts can access it. Before making such changes, ensure that the resulting access patterns align with business requirements.

## Inherited Access

Remember that organization access is inherited down the hierarchy. An account with access to a parent organization automatically gains access to all of its descendants. This inheritance simplifies administration but requires careful consideration when structuring the organization hierarchy.

## Service Account Management

Service accounts authenticate using client certificates and are not subject to password expiration policies. Therefore, it is essential to:

- Implement a regular certificate rotation schedule
- Promptly revoke certificates for compromised or decommissioned service accounts
- Assign the minimum necessary permissions and organization access to service accounts

## Permission Combinations

The effective permissions of an account are determined by the combination of all assigned roles. When an account is assigned multiple roles, it gains all permissions from each role. This aggregation should be considered when designing roles to avoid unintentionally granting excessive permissions.

## Administration Best Practices

To effectively manage the Iris security model, system administrators should follow these best practices:

## Organization Hierarchy Design

Design the organization hierarchy to reflect the natural structure of the business while considering security boundaries. Avoid creating unnecessarily deep hierarchies that may complicate access management.

## Role Definitions

Create roles that align with job functions rather than individual users. This approach simplifies administration as users with similar responsibilities can be assigned the same roles.

## Principle of Least Privilege

Assign accounts the minimum organization access and permissions necessary to perform their functions. This practice limits the potential impact of compromised accounts.

## Regular Access Reviews

Conduct periodic reviews of account access to ensure that permissions remain appropriate as user responsibilities change. Pay particular attention to accounts with `AllOrganizations` access.

## Security Auditing

Monitor security-related events, such as authentication failures, permission denials, and administrative actions. These logs can help identify potential security issues or misconfigurations in the access control system.