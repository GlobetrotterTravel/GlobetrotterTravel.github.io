---
layout: default
title: GetOrganizationsBySet
parent: Iris API functions
has_children: false
has_toc: false
---

# GetOrganizationsBySet

Retrieves all organizations matching a specific set definition.

## Syntax

```jyro
GetOrganizationsBySet(setName)
```

## Parameters

- **setName** (string): The name of the set definition to match

## Returns

- **array**: Array of Organization objects matching the set criteria
- **array** (empty): If the set exists but no organizations match
- **null**: If the set is not found or targets the wrong resource type (not Organization)

## Description

Evaluates a set definition against all organizations in the tenant and returns those that match the set's criteria. Set definitions are pre-configured rules that can filter organizations based on various conditions like facet values, field values, or other properties.

This is useful for targeting specific groups of organizations for notifications, reports, or bulk operations.

## Examples

```jyro
# Get all VIP customer organizations
var vipOrgs = GetOrganizationsBySet("VIP Customers")

if vipOrgs == null then
    Log("Warning", "Set not found or targets wrong resource type")
else if Length(vipOrgs) == 0 then
    Log("Information", "No organizations match this set")
else
    Log("Information", "Found " + Length(vipOrgs) + " VIP organizations")

    foreach org in vipOrgs do
        Log("Information", "  - " + org.name)
    end
end
```

```jyro
# Send notifications to enterprise customers
var enterpriseOrgs = GetOrganizationsBySet("Enterprise Tier")

if enterpriseOrgs != null and Length(enterpriseOrgs) > 0 then
    foreach org in enterpriseOrgs do
        # Get persons in this organization
        var persons = GetAllPersons()

        foreach person in persons do
            if person.organizationId == org.id then
                SendNotification(
                    person.emailAddress,
                    "Enterprise Feature Update",
                    "New features are available for your enterprise account!",
                    "Info",
                    "/features"
                )
            end
        end
    end
end
```

```jyro
# Generate report for specific organization segment
var activePartners = GetOrganizationsBySet("Active Partners")

if activePartners != null then
    var reportData = {
        "count": Length(activePartners),
        "organizations": activePartners,
        "generatedAt": Now()
    }

    var html = RenderTemplate("PartnerReport", reportData)
    SendEmail("reports@company.com", "Partner Report", html)
end
```

```jyro
# Compare organizations in different sets
var goldTier = GetOrganizationsBySet("Gold Tier")
var silverTier = GetOrganizationsBySet("Silver Tier")

if goldTier != null and silverTier != null then
    Log("Information", "Gold tier organizations: " + Length(goldTier))
    Log("Information", "Silver tier organizations: " + Length(silverTier))

    # Send upgrade offers to silver tier
    foreach org in silverTier do
        CreateArticle(
            "Upgrade Offers",
            "Upgrade to Gold for " + org.name,
            "Special upgrade pricing available for " + org.name
        )
    end
end
```
