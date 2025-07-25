---
layout: default
title: Role-Based Access Control
parent: Concepts
has_children: false
has_toc: false
permalink: /iris/concepts/role-based-access-control/
---

# Role-Based Access Control

Iris uses a role-based access control (RBAC) system to ensure that only authorised people and systems can read and write data.

The RBAC is made up of two main components: `Roles` and `Permissions`.

A `Permission` is made up of an `ItemType` identifier that represents the Iris resource the permission relates to, and a `CrudAction` (one of `Create`, `Read`, `Update` or `Delete`) that determines what the role can do with the resource.

Roles are made up of multiple permissions, each a pair of `ItemType` and `CrudAction`. 

Access is default-deny, and roles that do not have a particular permission present will prevent an action from being carried out.

## Default role

When a new tenant is created, a default "Tenant Administrator" role is created. This role has all permissions for all item types and can therefore carry out all operations within a tenant.



## Altering roles

