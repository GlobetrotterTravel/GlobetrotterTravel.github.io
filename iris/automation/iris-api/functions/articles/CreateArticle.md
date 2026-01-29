---
layout: default
title: CreateArticle
parent: Iris API functions
has_children: false
has_toc: false
---

# CreateArticle

Creates an article for display on the home page or news feed.

## Syntax

```jyro
CreateArticle(category, subject, description)
```

## Parameters

- **category** (string): Article category/tag (e.g., "Company News", "Updates", "Announcements")
- **subject** (string): Article title/subject
- **description** (string): Article content/description

## Returns

- **boolean**: `true` if article creation was initiated, `false` on error

## Description

Creates a news article that appears on the home page or news feed. Articles are created asynchronously (fire-and-forget) and will appear in the tenant's article list. Useful for automatically posting announcements when certain domain events occur.

## Examples

```jyro
# Announce new organization creation
AfterOrganizationCreated:
    CreateArticle(
        "Company News",
        "New Organization Created",
        "Welcome " + Data.Organization.Name + " to our platform!"
    )
```

```jyro
# Post system maintenance notice
CreateArticle(
    "System Updates",
    "Scheduled Maintenance Tonight",
    "The system will be unavailable for maintenance tonight from 10pm to 2am EST."
)
```

```jyro
# Announce new feature
CreateArticle(
    "Announcements",
    "New Reporting Feature Available",
    "We've added a new reporting feature that allows you to export data in multiple formats. Check out the Reports menu to get started!"
)
```

```jyro
# Conditional article creation
AfterPersonCreated:
    # Only announce VIP member joins
    var emailParts = Data.Person.EmailAddress.split("@")
    var domain = emailParts[1]

    if domain == "vip-partner.com" then
        CreateArticle(
            "Partner News",
            "New VIP Partner Member",
            "Please welcome " + Data.Person.FirstName + " " + Data.Person.LastName + " from our VIP partner organization!"
        )
    end
```

```jyro
# Weekly summary article
var orgs = GetAllOrganizations()
var activeCount = 0
foreach org in orgs do
    if org.isActive then
        activeCount = activeCount + 1
    end
end

CreateArticle(
    "Weekly Summary",
    "Platform Statistics",
    "Current active organizations: " + activeCount
)
```

## Best Practices

1. **Keep categories consistent** - Use a defined set of categories for better filtering
2. **Write clear subjects** - Subjects appear in lists and should be descriptive
3. **Keep descriptions concise** - Articles are meant for quick reading
4. **Use sparingly** - Avoid creating too many articles that overwhelm users

## Related Functions

- [BroadcastNotification](../notifications/BroadcastNotification.md) - For real-time alerts
- [SendNotification](../notifications/SendNotification.md) - For user-specific alerts
