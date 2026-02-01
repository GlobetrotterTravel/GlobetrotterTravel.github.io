---
layout: default
title: Create a dynamic endpoint
parent: Dynamic endpoints
grand_parent: Automation
nav_order: 10
permalink: /iris/automation/dynamic-endpoints/create-dynamic-endpoint/
---

# Create a dynamic endpoint
{: .no_toc }

Creating a dynamic endpoint extends the Iris API and allows unlimited control over endpoint behaviour.

## Create a dynamic endpoint

To create a new dynamic endpoint, open the `Automation & Workflow` navigation menu and click `Dynamic Endpoints`, then click `+ Create Endpoint`.

The `Create New Dynamic Endpoint` panel appears.

## Details tab

Populate the form:

10. `HTTP Method`: GET, POST, PUT, PATCH, or DELETE
20. `Name`: Friendly name of the endpoint
30. `Path`: The URL path for the endpoint. All endpoints start with `/api/dynamic`. It's recommended that endpoints are versioned for maintainability, so start with `/vx/` and then the url (e.g., `v1/org-summary`)
40. `Category`: When this endpoint is used for generating a report for an organization or person, select the appropriate group in this dropdown. Endpoints that belong to `Organization Report` or `Person Report` appear in the report menus for organizations and people respectively.
50. `Description`: Describe what the endpoint does
60. `Active`: Toggle this switch to enable or disable the endpoint.

## Authentication

10. `Requires Authentication`: Whether callers must be authenticated
30. `Add Role`: To restrict access to this endpoint to a list of roles, select each role that should be granted access and click `+ Add`. The role appears in the `Allowed Roles` section. To remove a lised role, click the `Trash Can` icon next to the role to remove.

## Script

Write the Jyro script that will run when the endpoint is called.

Use the special `Data` context keys:

`Data._payload`: the string data to return
`Data._contentType`: The content type of `Data._payload`
`Data._statusCode`: The HTTP status code to return
`Data._redirect`: An optional redirect URL

Click `Create`.

The endpoint becomes available at:

```
/api/dynamic/{path}
```