---
layout: default
title: RenderTemplate
parent: Iris API functions
has_children: false
has_toc: false
---

# RenderTemplate

Renders a Scriban template with provided data context.

## Syntax

```jyro
RenderTemplate(templateName, dataContext)
```

## Parameters

- **templateName** (string): The name of the template to render (must exist in the template store)
- **dataContext** (object): An object containing the data to pass to the template

## Returns

- **string**: The rendered HTML/text content, or empty string on error

## Description

Renders a stored Scriban template using the provided data context. Templates use the Scriban templating syntax and can access all properties of the data context object. This is commonly used for generating email content, reports, or other dynamic documents.

## Examples

```jyro
# Render a welcome email template
var emailData = {
    "person": {
        "firstName": "John",
        "lastName": "Doe",
        "email": "john@example.com"
    },
    "organization": {
        "name": "Acme Corp"
    }
}
var html = RenderTemplate("WelcomeEmail", emailData)
SendEmail("john@example.com", "Welcome to Acme Corp!", html)
```

```jyro
# Render a report template with current data
var reportData = {
    "organization": Data.Organization,
    "generatedAt": Now(),
    "generatedBy": GetCurrentUser().name
}
var report = RenderTemplate("MonthlyReport", reportData)
Log("Information", "Generated report: " + Length(report) + " characters")
```

```jyro
# Handle template not found
var html = RenderTemplate("NonExistentTemplate", {})
if html == "" then
    Log("Warning", "Template not found or render failed")
    # Fallback to simple message
    html = "<p>Default content</p>"
end
```

```jyro
# Render with array data for iteration in template
var data = {
    "title": "Active Organizations",
    "organizations": GetAllOrganizations()
}
var html = RenderTemplate("OrganizationList", data)
```

## Template Syntax (Scriban)

Templates use Scriban syntax. Here's a quick reference:

```html
<!-- Variable output -->
<p>Hello, {{ person.firstName }}!</p>

<!-- Conditionals -->
{{ if organization.isActive }}
    <span class="active">Active</span>
{{ else }}
    <span class="inactive">Inactive</span>
{{ end }}

<!-- Loops -->
<ul>
{{ for item in items }}
    <li>{{ item.name }}</li>
{{ end }}
</ul>

<!-- Filters -->
<p>{{ person.name | string.upcase }}</p>
<p>{{ createdAt | date.to_string "%Y-%m-%d" }}</p>
```

## Related Functions

- [SendEmail](../email/SendEmail.md) - Send the rendered content via email
- [GetCurrentUser](../context/GetCurrentUser.md) - Get current user for template context
