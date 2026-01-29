---
layout: default
title: DeleteSecret
parent: Iris API functions
has_children: false
has_toc: false
---

# DeleteSecret

Deletes a secret by its name. This is a permanent deletion from the database.

## Syntax

```jyro
DeleteSecret(secretName)
```

## Parameters

- **secretName** (string): The name of the secret to delete

## Returns

- **boolean**: true if successful, false if secret not found or deletion failed

## Description

Permanently removes a secret from the tenant's secret store by name (case-insensitive). The secret is completely deleted and cannot be recovered.

## Examples

```jyro
# Delete old secret
var success = DeleteSecret("OldApiKey")
if success then
    Log("Information", "Secret deleted successfully")
end
```

```jyro
# Validate before deleting
var secret = GetSecret("TemporaryToken")
if secret != null then
    var deleted = DeleteSecret("TemporaryToken")
    if not deleted then
        Log("Warning", "Failed to delete secret")
    end
end
```

```jyro
# Delete with error handling
var deleted = DeleteSecret("ExpiredKey")
if not deleted then
    Log("Warning", "Secret not found or could not be deleted")
end
```

```jyro
# Clean up multiple expired secrets
var expiredSecrets = ["OldToken1", "OldToken2", "OldToken3"]
foreach secretName in expiredSecrets do
    var result = DeleteSecret(secretName)
    if result then
        Log("Information", "Deleted: " + secretName)
    else
        Log("Warning", "Could not delete: " + secretName)
    end
end
```
