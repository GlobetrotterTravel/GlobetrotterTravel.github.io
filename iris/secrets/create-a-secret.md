---
layout: default
title: Create a secret
parent: Secrets
grand_parent: Globetrotter Iris
nav_order: 10
permalink: /iris/secrets/create-a-secret/
---

# Create a secret
{: .no_toc }

Creating a secret allows scripts to access login credentials and other sensitive information without exposing those credentials directly in code.

## Create a secret

Navigate to the `System` navigation menu and click `Secrets`, then click `+ Create Secret`. The `Create New Secret` dialog appears.

Populate the form:

1. **Name**: The unique identifier for the secret. It can contain mixed case and spaces.
2. **Description**: A description of what the secret represents.
3. **Value**: The secret string value. Once set, it cannot be read by again using the interface (but is available to scripts)
4. **Expires At**: To set up an expiring secret, populate this field.

Click `Save`. The secret is set.

> 🛈 Use the Iris API functions `SetSecret()`, `GetSecret()` and `DeleteSecret()` to manipulate secrets in scripts.
