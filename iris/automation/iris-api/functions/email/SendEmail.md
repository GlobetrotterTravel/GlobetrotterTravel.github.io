---
layout: default
title: SendEmail
parent: Iris API functions
has_children: false
has_toc: false
---

# SendEmail

Sends an email via the configured email service (Postmark).

## Syntax

```jyro
SendEmail(to, subject, htmlBody)
SendEmail(to, subject, htmlBody, textBody)
SendEmail(to, subject, htmlBody, textBody, tag)
```

## Parameters

- **to** (string): Recipient email address
- **subject** (string): Email subject line
- **htmlBody** (string): HTML body content
- **textBody** (string, optional): Plain text alternative body (defaults to empty)
- **tag** (string, optional): Tag for categorizing/tracking emails in Postmark

## Returns

- **boolean**: `true` if email was sent successfully, `false` otherwise

## Description

Sends an email through the Postmark email service. Supports HTML and plain text bodies. The email is sent asynchronously but the function waits for confirmation before returning. Tags can be used to categorize emails for analytics in the Postmark dashboard.

## Examples

```jyro
# Send a simple HTML email
var sent = SendEmail(
    "john@example.com",
    "Welcome to our platform!",
    "<h1>Welcome!</h1><p>Your account is ready.</p>"
)
if sent then
    Log("Information", "Welcome email sent successfully")
else
    Log("Error", "Failed to send welcome email")
end
```

```jyro
# Send email with rendered template
var html = RenderTemplate("WelcomeEmail", {
    "name": Data.Person.FirstName + " " + Data.Person.LastName
})
var sent = SendEmail(Data.Person.EmailAddress, "Welcome!", html)
```

```jyro
# Send email with both HTML and plain text
var htmlBody = "<h1>Monthly Report</h1><p>See attached report.</p>"
var textBody = "Monthly Report\n\nSee attached report."
var sent = SendEmail(
    "manager@company.com",
    "Monthly Report - " + Now(),
    htmlBody,
    textBody
)
```

```jyro
# Send email with tag for tracking
var sent = SendEmail(
    Data.Person.EmailAddress,
    "Password Reset Request",
    "<p>Click the link below to reset your password.</p>",
    "Click the link below to reset your password.",
    "password-reset"
)
```

```jyro
# Conditional email sending
AfterPersonCreated:
    # Only send welcome email for active persons
    if Data.Person.IsActive then
        var html = RenderTemplate("WelcomeEmail", {
            "person": Data.Person,
            "loginUrl": "https://app.example.com/login"
        })

        var sent = SendEmail(
            Data.Person.EmailAddress,
            "Welcome to the Platform!",
            html,
            null,
            "welcome"
        )

        if sent then
            Log("Information", "Welcome email sent to " + Data.Person.EmailAddress)
        end
    end
```

## Configuration

The email service must be configured with valid Postmark credentials in the application settings:

```json
{
  "Postmark": {
    "ServerToken": "your-postmark-server-token",
    "FromEmail": "noreply@yourdomain.com",
    "FromName": "Your App Name"
  }
}
```

## Related Functions

- [RenderTemplate](../templating/RenderTemplate.md) - Render email templates
- [SendNotification](../notifications/SendNotification.md) - Send in-app notifications
