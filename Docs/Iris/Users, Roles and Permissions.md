# Users, Service Accounts, Roles, and Permissions

This section describes how to manage access to potentially sensitive parts of your Iris instance. It covers how `roles` and `permissions` work, how to provide `users` and `subscribers` granular access to Iris data and functionality and how to divide Iris administrative tasks amongst several people.

## In this section

* [Users](#users)
    * [Managing users](#managing-users)
    * [Enabling Password Complexity Requirements](#enabling-password-complexity-requirements)
    * [Matching to a user record set](#matching-to-a-user-record-set)
* [Service Accounts](#service-accounts)
    * [Managing Service Accounts](#managing-service-accounts)
* [Roles and Permissions](#roles-and-permissions)
    * [Roles](#roles)
    * [Permissions](#permissions)
    * [Managing Roles and Permissions](#managing-roles-and-permissions)
    * [The `tenantAdmin` Role](#the-tenantadmin-role)
    * [Custom roles](#custom-roles)


## Users

A `user` is an authorized individual with access to Iris, uniquely identified by an email address. While email addresses need not be valid, Iris will attempt to send emails to the specified address. 

A valid username and password are required to access any information in Iris.

### Managing users

To create a user, specify a unique email address. The user will be created with no default password, and the user must set it upon first log in. 

Administrators can lock out a user, blocking Iris access regardless of valid credentials, or deactivate a user, fully excluding them from operational workflows.

### Enabling Password Complexity Requirements

Password complexity is managed via a regex pattern. To enforce password complexity, set the system property `user.password.complexityRegex` to the desired regex pattern. Passwords failing to meet this pattern will be rejected. Optionally, administrators may set `user.password.complexityDescription` to provide a description of the complexity rules.

### Matching to a user record set

The user table itself does not store any extended information about the user. For this reason it's recommended that a "person record" `entity` `definition` be created. This `definition` can either be standalone (using the default `set` for that `defnition`) or co-mingled with other person type records, using a criteria for the `set` (e.g. a `dimension` "Internal: Yes/No"). The only requirement is that a `property facet` is created on the definition that specifies the email address for the person.

The set is tied using two system properties: `user.set.name` specifies the set that should be used for extended information for users, and `user.set.emailAddressFacet` specifies which `facet` holds the email address value on which users are matched.

By default, users are assigned permission to edit their own entity record.

## Service Accounts

`Service accounts` provide a means for `subscribers` to access specific resources without requiring individual `user` credentials. Designed for automated access, these accounts are ideal for `subscribers` that need consistent, ongoing access to Iris data or functionality. Each `service account` is assigned a `role`, defining the scope of its access based on `permissions` associated with that `role`.

Each `service account` is secured with a unique SSL certificate fingerprint, which Iris holds. When a subscriber system presents an SSL certificate, Iris finds the matching fingerprint and allows access to resources specified by the `service account's` role. The `role-based access control` set up for `users` and `service accounts` is identical. For example, a service account could be granted `Read` access to data lists without `Create` or `Modify` permissions, restricting it to view-only access.

`Service accounts` have a defined activation and expiration date, ensuring they can only be used within a specific timeframe. Administrators can set these dates to align with contractual terms, compliance requirements, certificate expiry, or operational needs.

### Managing Service Accounts

Administrators create service accounts and assign them appropriate roles. Configuration involves capturing and recording the fingerprint for authentication, defining the validity period, and assigning permissions through role selection. Administrators can deactivate service accounts, immediately revoking access, or delete them when they are no longer needed.

## Roles and Permissions

In Iris, roles and permissions provide a framework to control access to system components at a granular level. Roles define groups of permissions for specific actions on resources, while permissions specify which actions can be taken by users or service accounts assigned to those roles.

### Roles

Roles represent sets of permissions that are applied to users or service accounts within a tenant. Each role is unique to a tenant, allowing administrators to create roles tailored to the tenant's needs. Roles serve as containers for permissions, simplifying access management by grouping permissions under identifiable labels (e.g., `Admin`, `Viewer`).

Roles are assigned by administrators to users or service accounts. Once assigned, the role's permissions govern what the user or service account can access and modify within Iris.

### Permissions

Permissions specify the actions a role can perform on designated system components. Permissions are defined by the following capabilities:
- **Read**: Allows viewing of a resource.
- **Create**: Allows creation of new records.
- **Delete**: Allows deletion of existing records.
- **Modify**: Allows editing or updating of existing records.

Permissions apply to specific components within Iris, such as lists, reports, or configurations. By setting permissions on a role, administrators can control exactly what each role can do with each component. For instance, a `Manager` role may have `Read` and `Modify` permissions on reports but lack `Delete` permissions.

When creating new component definitions, the difference between `Create` and `Modify` permissions is nuanced. The `Create` permission allows a user to create a "shell" structure of a component, while the Modify permission allows the shell to be filled.

For example, a Hybrid List and associated categories may be initially created using the `Create` permission, however `Modify` permission is required to add any items or change any of the categories once the Hybrid List is created. It is recommended that unless you need granular permissions on components, Create and Modify permissions are always added together.

### Managing Roles and Permissions

### The `tenantAdmin` Role

Upon tenant setup, a default user, `admin`, is automatically created with the role `tenantAdmin`. The `tenantAdmin` role has full authority within the tenant, including:

- **User management**: adding and removing users.
- **Role management**: creating, modifying, and deleting roles.
- **Tenant settings**: adjusting tenant properties.
- **Permission management**: assigning and configuring permission lists within roles.
- **Role assignment**: assigning and unassigning roles to users, including the `tenantAdmin` role itself.
- **Component management**: Creating classes, definitions and relationships for components.
- **Record management**: creating, modifying and deleting all records.

While this role can be assigned to other `users`, at least one `user` within each tenant must hold the `tenantAdmin` role. Iris will not allow the removal of the `tenantAdmin` role from a `user` if there are no other `users` assigned that role.

### Custom roles

In addition to the default roles, custom roles can be created to allow flexible access delegation across the system. When creating a new role, specify its name and select the necessary permissions for each section that the role should cover. Assigning this role to users will apply the permissions set within it, enabling them to perform actions according to the role's configuration.

Permissions within roles are **additive**: if a user has multiple roles with conflicting permissions, the highest level of access from any assigned role will take precedence.

