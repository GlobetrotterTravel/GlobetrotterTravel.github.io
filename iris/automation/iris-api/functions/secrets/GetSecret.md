---
layout: default
title: GetSecret
parent: Iris API functions
has_children: false
has_toc: false
---

# GetSecret

Retrieves a decrypted secret value by its name. The secret must be active and not expired.

## Syntax

```jyro
GetSecret(secretName)
```

## Parameters

- **secretName** (string): The name of the secret to retrieve

## Returns

- **string**: The decrypted secret value, or null if not found, inactive, or expired

## Description

Retrieves a secret from the tenant's secret store by name (case-insensitive). The secret must be:
- Active (`IsActive = true`)
- Not expired (`ExpiresAt` is null or in the future)

The returned value is automatically decrypted before being returned to the script.

Take care not to output the secret value where it may be compromised. In particular, take care around outputting it via:

- The `Data` context
- The `Log()` function
- As a facet value or other record-data string
- The wire when sending data via `InvokeRestMethod()`

## Examples

```jyro
# Retrieve API key
var apiKey = GetSecret("ThirdPartyApiKey")
if apiKey != null then
    Log("Information", "Retrieved API key successfully")
end
```

```jyro
# Use credentials with error handling
var credentials = GetSecret("DatabaseConnectionString")
if credentials == null then
    Log("Error", "Failed to retrieve credentials")
    CancelAction("Missing required credentials")
end
```

```jyro
# Conditional logic based on secret availability
var apiToken = GetSecret("OptionalServiceToken")
if apiToken != null then
    # Call external API with token
    Log("Information", "Using authenticated API access")
else
    # Proceed without token
    Log("Warning", "Proceeding with anonymous access")
end
```
