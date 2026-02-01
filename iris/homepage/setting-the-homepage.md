---
layout: default
title: Setting the homepage
parent: Home
grand_parent: Globetrotter Iris
nav_order: 10
permalink: /iris/homepage/setting-the-homepage/
---

# Setting the homepage
{: .no_toc }

The homepage is a dynamic endpoint that returns an HTML document to display for users when they navigate to `/`. The Iris homepage setup can range from very simple static pages to pages of almost unlimited complexity.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Setting up a basic homepage

The simplest homepage is a static HTML page. To set up a static HTML page:

Navigate to `Automation & Workflow` -> `Dynamic Endpoints` and click `+ Create Endpoint`. The `Create New Dynamic Endpoint` dialog appears. Populate the form.

`Details` tab:

1. `HTTP Method`: GET
2. `Name`: "Homepage" or similar
3. `Path`: `/api/dynamic/home`
4. `Category`: Leave as `--None--`
5. `Description`: The Iris homepage.

`Authentication` tab:

Ensure that `Requires Authentication` is set to `On`.

`Script` tab:

Replace the script with this one.

```jyro
Data._payload = RenderTemplate("home", Data)
Data._contentType = "text/html"
Data._statusCode = 200
```

Click `Create`.

Navigate to `Automation & Workflow` -> `Templates` and click `+ Create Template`. The `Create New Template` panel appears. Populate the form.

`General` tab:

1. `Name`: Home
2. `Category`: Homepage
3. `Description`: The Iris homepage report

`Template` tab:

Replace the template with this one.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Globetrotter Iris</title>
    </head>
    <body>
        <h1>Welcome to Globetrotter Iris!</h1>
    </body>
</html>
```

Navigate to `Home`. The static HTML homepage appears.

## Setting up a dynamic homepage

A dynamic homepage retrieves live data and renders it using a Scriban template. The technique is the same as for a static homepage, however the dynamic endpoint can be used to first retrieve, aggregate, collate, transform and present any type of internal or external data before it is sent to the template for rendering.

To set up a dynamic homepage:

Navigate to `Automation & Workflow` -> `Dynamic Endpoints` and click `+ Create Endpoint`. The `Create New Dynamic Endpoint` dialog appears. Populate the form.

`Details` tab:

1. `HTTP Method`: GET
2. `Name`: "Homepage" or similar
3. `Path`: `/api/dynamic/home`
4. `Category`: Leave as `--None--`
5. `Description`: The Iris homepage.

`Authentication` tab:

Ensure that `Requires Authentication` is set to `On`.

`Script` tab:

Replace the script with this one.

```jyro
var user = GetCurrentUser()
var tenant = GetCurrentTenant()
var orgs = GetAllOrganizations()
var persons = GetAllPersons()

Data.model = {
    "user": user,
    "tenant": tenant,
    "totalOrganizations": Length(orgs),
    "totalPersons": Length(persons),
    "recentOrganizations": Take(Reverse(orgs), 5)
}

Data._payload = RenderTemplate("home", Data.model)
Data._contentType = "text/html"
Data._statusCode = 200
```

Click `Create`.

Navigate to `Automation & Workflow` -> `Templates` and click `+ Create Template`. The `Create New Template` panel appears. Populate the form.

`General` tab:

1. `Name`: Home
2. `Category`: Homepage
3. `Description`: The Iris homepage report

`Template` tab:

Replace the script with this one.

{% raw %}
```html
<!DOCTYPE html>
<html>
    <head>
        <title>{{ tenant.name }} - Iris</title>
    </head>
    <body>
        <h1>Welcome, {{ user.name }}!</h1>
        <p>Organizations: {{ totalOrganizations }} | People: {{ totalPersons }}</p>

        <h2>Recent Organizations</h2>
        {{ for org in recentOrganizations }}
        <div>
            <strong>{{ org.name }}</strong>
            <p>{{ org.description }}</p>
        </div>
        {{ end }}
    </body>
</html>
```
{% endraw %}

Navigate to `Home`. The dynamic homepage appears, showing the current user's name and live data from Iris.

### How it works

The script builds a data model by calling Iris API functions. Any function available in the Iris API can be used, including:

- `GetAllOrganizations()`, `GetAllPersons()` - Retrieve records
- `GetAllFacetDefinitions()`, `GetOrganizationFacetInstance()` - Work with facets
- `GetCurrentUser()`, `GetCurrentTenant()` - Access context information
- `InvokeRestMethod()` - Fetch real-time data from external APIs

The data model is passed to `RenderTemplate()`, which renders the Scriban template. The template accesses model properties directly (e.g., {% raw %}`{{ user.name }}`{% endraw %}) and can iterate over collections using {% raw %}`{{ for }}`{% endraw %} loops.

This pattern allows you to build dashboards that combine Iris data with information from CRM systems, support platforms, or any other API-accessible source.

You can call any function in the Iris API as well as the Jyro standard library (including InvokeRestMethod). This pattern allows you to present powerful data to your users.