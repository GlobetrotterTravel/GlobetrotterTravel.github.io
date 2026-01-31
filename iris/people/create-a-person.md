---
layout: default
title: Create a person
parent: People
grand_parent: Globetrotter Iris
nav_order: 10
permalink: /iris/people/create-a-person/
---

# Create a person
{: .no_toc }

To create a person, open the `Resources` navigation menu and click `People`.

 In the `All Orgs` dropdown, select the organization to which the new person will be assigned. The list of existing people for that organization is shown.

 Click the `+` button. The `Create New Person` dialog appears.

## Details tab

Populate the `Details` tab:

1. **First Name**: Set an optional first name for the new person.
2. **Last Name**: Set the person's last name.
3. **Email Address**: Set the person's email address. It must be unique across all people.
4. **User Principal Name (UPN)**: If the person will have a direct login to Iris, set the User Principal Name (UPN) here (normally the same as the email address).

> 🛈 Do not assign a UPN until you have read and understood the section on roles and security.

5. **Organizations**: The current organization is automatically added. To assign the new person to additional organizations, click `+ Add Organization` and select the organization. Repeat this step for additional organizations.

Ignore the `Settings` tab for now, and click `Save`.

The person is created.

## Roles tab

If the User Principal Name (UPN) was set, the `Roles tab` is active. 

> 🛈 To set roles, the UPN must be set, and the person saved. Once the person is saved, then you can go back into the person to set roles.

In `Assigned Roles` a list of roles is shown, along with the role scope.

To assign a new role, select it from the `Assign New Role` dropdown and click `+ Assign`.

To remove a role from a person, locate the role in the `Assigned Roles` list and click the `Trash Can` icon.

> 🛈 Do not assign roles until you have read and understood the section on roles and security.

## Other tabs

The remaining tabs provide configured facet instances for data population.