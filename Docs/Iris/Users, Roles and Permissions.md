# Users, Roles, and Permissions

This section describes how to manage access to potentially sensitive parts of your Iris instance. It covers how roles work, how permissions are managed and how to divide administration of Iris among several users.

## In this section

* [Roles](#roles)
    * [The `tenantAdmin` Role](#the-tenantadmin-role)
    * [Custom roles](#custom-roles)
* [Sections](#sections)
* [Permissions](#permissions)
## Roles

### The `tenantAdmin` Role

Upon tenant setup, a default user, `admin`, is automatically created with the role `tenantAdmin`. The `tenantAdmin` role has full authority within the tenant, including:

- User management: adding and removing users.
- Role management: creating, modifying, and deleting roles.
- Tenant settings: adjusting tenant properties.
- Permission management: assigning and configuring permission lists within roles.
- Role assignment: assigning and unassigning roles to users, including the `tenantAdmin` role itself.

While this role can be assigned to other users, at least one user within each tenant must hold the `tenantAdmin` role. Iris will not allow the removal of the `tenantAdmin` role from a user if there are no other users assigned that role.

### Custom roles

In addition to the default roles, custom roles can be created to allow flexible access delegation across the system. When creating a new role, specify its name and select the necessary permissions for each section that the role should cover. Assigning this role to users will apply the permissions set within it, enabling them to perform actions according to the role's configuration.

Permissions within roles are **additive**: if a user has multiple roles with conflicting permissions, the highest level of access from any assigned role will take precedence.

## Sections

A `section` defines a distinct area within Iris, such as `User`, `Field`, or `Hybrid List`. Each section has its own set of permissions that can be granted, allowing tenants to distribute specific administrative responsibilities across different users.

## Permissions

Permissions define the specific actions that roles can perform within each section:

- **`canRead`**: Allows read-only access to records within the designated section.
- **`canWrite`**: Allows modification of existing records. Enabling `canWrite` will also automatically enable `canRead` to allow context for modifications.
- **`canCreate`**: Permits the creation of new records within the section.
- **`canDelete`**: Grants the ability to delete records within the section. Enabling `canDelete` will also automatically enable `canRead` to allow context for deletions.
