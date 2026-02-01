---
layout: default
title: Templates
parent: Automation
grand_parent: Globetrotter Iris
nav_order: 50
permalink: /iris/automation/templates/
---

# Templates

Templates allow you to generate dynamic content by combining static text with data from your scripts. Iris supports three types of templates: Scriban templates for general rendering, email templates for notifications, and articles for dashboard content.

## Scriban Templates

Scriban templates are the foundation for dynamic content generation. They use a simple, powerful syntax to merge data with template text.

### What Is Scriban?

Scriban is a fast, lightweight templating language. It allows you to:

- Insert variable values into text
- Use conditionals (if/else)
- Loop over collections
- Format data (dates, numbers)
- Apply filters and transformations

### Creating a Scriban Template

1. Navigate to `Administration` > `Scripting` > `Templates`
2. Click `+ Create Template`
3. Configure:
   - **Name**: Unique template name
   - **Description**: What the template generates
   - **Category**: Organization category (e.g., "Email", "Report")
   - **Template Content**: Your Scriban template
   - **Is Active**: Enable/disable
4. Click `Save`

### Basic Scriban Syntax

**Variable Output**:
{% raw %}
```
Hello, {{ name }}!
```
{% endraw %}

**Conditionals**:
{% raw %}
```
{{ if is_active }}
  Status: Active
{{ else }}
  Status: Inactive
{{ end }}
```
{% endraw %}

**Loops**:
{% raw %}
```
{{ for org in organizations }}
  - {{ org.name }} ({{ org.id }})
{{ end }}
```
{% endraw %}

**Filters**:
{% raw %}
```
{{ name | upcase }}           <!-- JOHN SMITH -->
{{ created_at | date.to_string "%Y-%m-%d" }}  <!-- 2024-01-15 -->
{{ amount | math.round 2 }}   <!-- 99.99 -->
```
{% endraw %}

### Using Templates in Scripts

Use `RenderTemplate()` to render a template with data:

```jyro
var data = {
  name: "John Smith",
  organization: org.name,
  date: Now()
}

var html = RenderTemplate("WelcomeEmail", data)

# Use the rendered content
SendEmail(
  "john@example.com",
  "Welcome to " + org.name,
  html,
  null,
  "welcome"
)
```

### Template Examples

**Simple Greeting**:
{% raw %}
```
Hello {{ name }},

Welcome to {{ organization_name }}!

Best regards,
The Team
```
{% endraw %}

**With Conditionals**:
{% raw %}
```
Hello {{ name }},

{{ if is_new_user }}
Welcome! Here are some tips to get started:
- Complete your profile
- Review the documentation
- Contact support if you need help
{{ else }}
Welcome back! Here's what's new since your last visit:
{{ for item in updates }}
- {{ item.title }}
{{ end }}
{{ end }}

Best regards,
The Team
```
{% endraw %}

**Data Table**:
{% raw %}
```
Organization Report
Generated: {{ generated_at | date.to_string "%Y-%m-%d %H:%M" }}

| Name | Status | Children |
|------|--------|----------|
{{ for org in organizations }}
| {{ org.name }} | {{ if org.is_active }}Active{{ else }}Inactive{{ end }} | {{ org.child_count }} |
{{ end }}

Total: {{ organizations | array.size }} organizations
```
{% endraw %}

## Email Templates

Email templates combine Scriban rendering with the `SendEmail()` function to send formatted notifications.

### SendEmail Function

```jyro
SendEmail(to, subject, htmlBody, textBody, tag)
```

**Parameters**:
- `to`: Recipient email address
- `subject`: Email subject line
- `htmlBody`: HTML content (can be rendered from template)
- `textBody`: Plain text alternative (optional, can be null)
- `tag`: Email tag for tracking (optional)

### Creating an Email Template

**Template** (name: "WelcomeEmail"):
{% raw %}
```html
<!DOCTYPE html>
<html>
<head>
  <style>
    body { font-family: Arial, sans-serif; }
    .header { background: #2196F3; color: white; padding: 20px; }
    .content { padding: 20px; }
    .footer { background: #f5f5f5; padding: 10px; font-size: 12px; }
  </style>
</head>
<body>
  <div class="header">
    <h1>Welcome to {{ organization_name }}</h1>
  </div>
  <div class="content">
    <p>Hello {{ first_name }},</p>
    <p>Your account has been created successfully.</p>
    {{ if has_temporary_password }}
    <p><strong>Temporary Password:</strong> {{ temp_password }}</p>
    <p>Please change this password after your first login.</p>
    {{ end }}
    <p>
      <a href="{{ login_url }}">Click here to log in</a>
    </p>
  </div>
  <div class="footer">
    <p>{{ organization_name }} | {{ support_email }}</p>
  </div>
</body>
</html>
```
{% endraw %}

**Script using the template**:
```jyro
AfterPersonCreated:
  var templateData = {
    first_name: Data.person.firstName,
    organization_name: "Acme Corporation",
    has_temporary_password: true,
    temp_password: "TempPass123!",
    login_url: "https://acme.iriscloud.app/login",
    support_email: "support@acme.com"
  }

  var htmlBody = RenderTemplate("WelcomeEmail", templateData)

  var textBody = "Hello " + Data.person.firstName + ", your account is ready. Log in at: https://acme.iriscloud.app/login"

  SendEmail(
    Data.person.emailAddress,
    "Welcome to Acme Corporation",
    htmlBody,
    textBody,
    "welcome"
  )

  Log("Information", "Welcome email sent to: " + Data.person.emailAddress)
```

### Email Best Practices

**1. Always Include Plain Text Alternative**:
```jyro
var htmlBody = RenderTemplate("NotificationHTML", data)
var textBody = RenderTemplate("NotificationText", data)

SendEmail(to, subject, htmlBody, textBody, tag)
```

**2. Use Inline Styles**:
Email clients have limited CSS support. Use inline styles:
```html
<p style="color: #333; font-size: 14px;">Content here</p>
```

**3. Test Thoroughly**:
Test your emails in multiple clients (Gmail, Outlook, Apple Mail) before sending to users.

**4. Include Unsubscribe Information**:
{% raw %}
```html
<p style="font-size: 10px;">
  You're receiving this because you have an account at {{ organization_name }}.
</p>
```
{% endraw %}

## Articles

Articles are content items displayed on the dashboard. Use them for announcements, news, and important information.

### Creating Articles

Use `CreateArticle()` in your scripts:

```jyro
CreateArticle(category, subject, description)
```

**Parameters**:
- `category`: Article category (e.g., "Company News", "System Updates")
- `subject`: Article title
- `description`: Article body content (can be HTML)

### Example: System Announcement

```jyro
# Scheduled script for weekly system update announcements

CreateArticle(
  "System Updates",
  "Weekly Maintenance - " + Now().format("MMMM d, yyyy"),
  "<p>Scheduled maintenance will occur this weekend.</p>" +
  "<ul>" +
  "<li>Saturday 2:00 AM - 4:00 AM: Database optimization</li>" +
  "<li>System may be briefly unavailable</li>" +
  "</ul>" +
  "<p>Contact support if you have questions.</p>"
)

Log("Information", "Weekly maintenance article created")
```

### Example: Dynamic News Article

```jyro
AfterOrganizationCreated:
  # Create welcome article when new organization joins
  CreateArticle(
    "Company News",
    "Welcome " + Data.organization.name + "!",
    "<p>We're excited to announce that <strong>" + Data.organization.name + "</strong> " +
    "has joined our platform.</p>" +
    "<p>Please join us in welcoming them to the community!</p>"
  )
```

### Article Categories

Common categories to organize your articles:

| Category | Use For |
|----------|---------|
| Company News | Organization announcements, new partnerships |
| System Updates | Maintenance notices, feature releases |
| Tips & Tricks | User guidance, best practices |
| Policy Updates | Compliance, terms changes |

### Rendering Articles with Templates

For complex article content, use Scriban templates:

**Template** (name: "WelcomeArticle"):
{% raw %}
```html
<div class="welcome-article">
  <p>We're excited to welcome <strong>{{ org_name }}</strong> to our platform!</p>

  {{ if description }}
  <p><em>{{ description }}</em></p>
  {{ end }}

  <h3>Quick Facts</h3>
  <ul>
    <li>Industry: {{ industry }}</li>
    <li>Location: {{ location }}</li>
    <li>Size: {{ employee_count }} employees</li>
  </ul>

  <p>Please join us in welcoming them to the community!</p>
</div>
```
{% endraw %}

**Script**:
```jyro
AfterOrganizationCreated:
  # Get facet data
  var industryFacet = GetOrganizationFacetInstance(
    Data.organization.id,
    GetFacetDefinitionByName("Industry")
  )

  var locationFacet = GetOrganizationFacetInstance(
    Data.organization.id,
    GetFacetDefinitionByName("Location")
  )

  var templateData = {
    org_name: Data.organization.name,
    description: Data.organization.description,
    industry: industryFacet != null ? industryFacet.values[0] : "Not specified",
    location: locationFacet != null ? locationFacet.values[0] : "Not specified",
    employee_count: "Growing"
  }

  var content = RenderTemplate("WelcomeArticle", templateData)

  CreateArticle(
    "Company News",
    "Welcome " + Data.organization.name,
    content
  )
```

## Template Management

### Organizing Templates

Use categories to organize your templates:

- **Email**: Email notification templates
- **Report**: Report and document templates
- **Article**: Dashboard article templates
- **Integration**: Templates for external systems

### Versioning Templates

Templates are stored in the database and can be updated at any time. Consider:

1. **Test Changes**: Test template changes in a development environment first
2. **Backup**: Keep copies of working templates before making changes
3. **Document**: Include comments in your templates explaining their purpose

### Deactivating Templates

Deactivate templates that are no longer needed:

1. Navigate to the template
2. Set **Is Active** to `false`
3. Scripts calling the template will fail gracefully

> 🛈 Deactivated templates are preserved but won't be used. Delete templates only when you're certain they're no longer referenced.

## Scriban Reference

### Variables

{% raw %}
```
{{ variable_name }}
{{ object.property }}
{{ array[0] }}
```
{% endraw %}

### Conditionals

{% raw %}
```
{{ if condition }}
  content
{{ else if other_condition }}
  other content
{{ else }}
  default content
{{ end }}
```
{% endraw %}

### Loops

{% raw %}
```
{{ for item in collection }}
  {{ item.property }}
{{ end }}

{{ for item in collection limit:5 }}
  First 5 items only
{{ end }}
```
{% endraw %}

### Built-in Filters

| Filter | Example | Result |
|--------|---------|--------|
| `upcase` | {% raw %}`{{ "hello" | upcase }}`{% endraw %} | HELLO |
| `downcase` | {% raw %}`{{ "HELLO" | downcase }}`{% endraw %} | hello |
| `capitalize` | {% raw %}`{{ "hello world" | capitalize }}`{% endraw %} | Hello world |
| `size` | {% raw %}`{{ items | array.size }}`{% endraw %} | 5 |
| `first` | {% raw %}`{{ items | array.first }}`{% endraw %} | First item |
| `last` | {% raw %}`{{ items | array.last }}`{% endraw %} | Last item |
| `date.to_string` | {% raw %}`{{ date | date.to_string "%Y-%m-%d" }}`{% endraw %} | 2024-01-15 |
| `math.round` | {% raw %}`{{ 3.14159 | math.round 2 }}`{% endraw %} | 3.14 |

### Comments

{% raw %}
```
{{# This is a comment and won't appear in output #}}
```
{% endraw %}

### Whitespace Control

{% raw %}
```
{{~ variable ~}}   <!-- Trims whitespace on both sides -->
{{- variable -}}   <!-- Removes newlines on both sides -->
```
{% endraw %}

## Next Steps

- [Event Hooks](/iris/automation/event-hooks/) - Use templates in event hooks
- [Dynamic Endpoints](/iris/automation/dynamic-endpoints/) - Return rendered content from APIs
- [Script Scheduling](/iris/automation/script-scheduling/) - Generate scheduled reports with templates
