---
layout: default
title: Secrets
parent: Globetrotter Iris
nav_order: 65
has_children: true
has_toc: false
permalink: /iris/secrets/
---

# Secrets
{: .no_toc }

Secrets provide a secure way to store sensitive information such as API keys, passwords, and other credentials that your scripts and automations need to access. By centralising secrets management in Iris, you can reference credentials in your code without exposing their values directly, reducing security risks and simplifying credential rotation.

## How secrets work

Secrets are stored securely and can only be accessed programmatically through the Iris API. When you create a secret, the value is encrypted and cannot be retrieved through the user interface—only scripts running within Iris can read secret values using the `GetSecret()` function.

Each secret has a unique name that serves as its identifier. Scripts reference secrets by name, allowing you to update credential values without modifying your code. This separation between the secret reference and its value makes it straightforward to rotate credentials or update configurations across all dependent scripts simultaneously.

## Expiration

Secrets can optionally have an expiration date. This is useful for credentials that need to be rotated regularly, or for temporary access tokens that should automatically become invalid after a certain period. Expired secrets will no longer return values when accessed through the API.

## API functions

Iris provides three API functions for working with secrets programmatically:

- **`GetSecret()`**: Retrieves the value of a secret by name
- **`SetSecret()`**: Creates or updates a secret value
- **`DeleteSecret()`**: Removes a secret from the system

These functions enable your automation scripts to manage credentials dynamically while keeping sensitive values secure.

## In this section

- [Create a secret](/iris/secrets/create-a-secret/): Store a new credential or sensitive value
- [Reset a secret](/iris/secrets/reset-a-secret/): Update an existing secret's value
- [Delete a secret](/iris/secrets/delete-a-secret/): Remove a secret from the system
