---
layout: default
title: SendNotification
parent: Iris API functions
has_children: false
has_toc: false
---

# SendNotification

Sends a real-time notification to a specific user via SignalR.

## Syntax

```jyro
SendNotification(userId, title, message, type, link)
```

## Parameters

- **userId** (string): Target user ID (e.g., "user:john@example.com" or "john@example.com")
- **title** (string): Notification title
- **message** (string): Notification message body
- **type** (string): Notification type: "Info", "Success", "Warning", or "Error"
- **link** (string, optional): Navigation link (can be null)

## Returns

- **boolean**: true on success

## Description

Sends a notification to a specific user. The notification is:
- Persisted to the database
- Pushed in real-time via SignalR WebSocket
- Displayed in the user's notification panel
- Shown as a toast popup if the user is online

Execution is fire-and-forget and won't block script execution.

## Examples

```jyro
# Send welcome notification
SendNotification(
    Data.Person.EmailAddress,
    "Welcome to the platform!",
    "Your account has been successfully created. Click here to get started.",
    "Success",
    "/profile"
)
```

```jyro
# Send notification to admin
SendNotification(
    "admin@company.com",
    "New Person Created",
    "A new person was added: " + Data.Person.FirstName + " " + Data.Person.LastName,
    "Info",
    "/persons/" + Data.Person.Id
)
```

```jyro
# Send error notification
var success = SendNotification(
    Data.Request.user.principalId,
    "Operation Failed",
    "The requested operation could not be completed. Please try again later.",
    "Error",
    null
)

if success then
    Log("Information", "Error notification sent to user")
end
```

```jyro
# Conditional VIP notification
var emailParts = Data.Person.EmailAddress.split("@")
var domain = emailParts[1]

if domain == "vip-company.com" then
    SendNotification(
        Data.Person.EmailAddress,
        "VIP Welcome!",
        "Welcome to our VIP program. Enjoy exclusive benefits!",
        "Success",
        "/vip-benefits"
    )

    SendNotification(
        "sales@company.com",
        "VIP Customer Signup",
        "VIP customer joined: " + Data.Person.FirstName + " " + Data.Person.LastName,
        "Info",
        "/persons/" + Data.Person.Id
    )
end
```
