---
layout: default
title: SetSecret
parent: Iris API functions
has_children: false
has_toc: false
---

# SetSecret

Creates or updates a secret by name. The secret value is automatically encrypted before storage.

## Syntax

```jyro
SetSecret(secretName, secretValue, description)
```

## Parameters

- **secretName** (string): The name of the secret
- **secretValue** (string): The plaintext value to encrypt and store
- **description** (string, optional): Description of the secret's purpose

## Returns

- **boolean**: true if successful, false otherwise

## Description

Smart detection: Creates a new secret if one with the given name doesn't exist, or updates the existing secret's value if it does.

The secret value is automatically encrypted using the system's encryption service before being stored in the database. The description parameter is optional and can be null or omitted.

## Examples

```jyro
# Create a new secret
var success = SetSecret("ApiKey", "sk_live_abc123", "Third-party API key")
if success then
    Log("Information", "Secret created successfully")
end
```

```jyro
# Update existing secret (description optional)
var updated = SetSecret("ApiKey", "sk_live_xyz789", null)
if not updated then
    Log("Error", "Failed to update secret")
    CancelAction("Secret update failed")
end
```

```jyro
# Create temporary credential
var created = SetSecret("TempToken", "token123", "Temporary access token")
if created then
    Log("Information", "Temporary secret created")
end
```

```jyro
# Set secret from external data
var newApiKey = Data.Request.body.apiKey
if newApiKey != null then
    var saved = SetSecret("ExternalApiKey", newApiKey, "API key from external system")
    if saved then
        Log("Information", "External API key saved securely")
    end
end
```

## Advanced Pattern: Caching Authentication Tokens

This pattern demonstrates caching a JWT (or other authentication token) to avoid repeated login requests to an external API:

```jyro
# External API configuration
var apiUrl = "https://api.external-service.com"
var headers = { "Content-Type": "application/json" }

# Try to use cached JWT first
var cachedJwt = GetSecret("ExternalApiJWT")
var apiResponse

if cachedJwt != null then
    Log("Information", "Using cached authentication token")

    headers = {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + cachedJwt
    }

    apiResponse = InvokeRestMethod(apiUrl + "/api/data", "GET", headers, null)
end

# If no cached token or authentication failed, obtain new token
if cachedJwt == null or apiResponse.statusCode == 401 then
    Log("Information", "Cached token invalid or expired, obtaining new token")

    # Retrieve stored credentials
    var username = GetSecret("ExternalApiUsername")
    var password = GetSecret("ExternalApiPassword")

    if username == null or password == null then
        Log("Error", "Missing API credentials")
        CancelAction("External API credentials not configured")
    end

    # Authenticate and obtain new token
    var loginPayload = {
        "username": username,
        "password": password
    }

    var authResponse = InvokeRestMethod(
        apiUrl + "/auth/login",
        "POST",
        headers,
        loginPayload
    )

    if authResponse.statusCode != 200 then
        Log("Error", "Failed to authenticate with external API")
        CancelAction("External API authentication failed")
    end

    # Extract JWT from response
    var newJwt = authResponse.content.token

    if newJwt == null then
        Log("Error", "No token returned from authentication")
        CancelAction("Invalid authentication response")
    end

    # Cache the new JWT for future requests
    var cached = SetSecret("ExternalApiJWT", newJwt, "Cached authentication token")

    if cached then
        Log("Information", "New authentication token cached successfully")
    else
        Log("Warning", "Failed to cache authentication token")
    end

    # Retry the original request with new token
    headers = {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + newJwt
    }

    apiResponse = InvokeRestMethod(apiUrl + "/api/data", "GET", headers, null)
end

# Use the API response
if apiResponse.statusCode == 200 then
    var data = apiResponse.content
    Log("Information", "Successfully retrieved data from external API")
    # Process data...
else
    Log("Error", "Failed to retrieve data: " + apiResponse.statusCode)
end
```
