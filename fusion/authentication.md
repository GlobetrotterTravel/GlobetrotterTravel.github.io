---
layout: default
title: Authentication
parent: Globetrotter Fusion
nav_order: 20
permalink: /fusion/authentication/
---

# Authentication
{: .no_toc }

Fusion authenticates callers using an API key carried in the `X-API-Key` HTTP request header. Each key is bound to a **service account** - a named identity managed within Fusion - and confers the access granted to that account.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## The X-API-Key header

Set the header on every request:

| Header | Value |
|---|---|
| `X-API-Key` | The API key issued to the service account. |

The value must match the issued key exactly. Only one key may be presented per request.

{: .warning }

API keys are credentials. Fusion must only be accessed over HTTPS, and keys must be stored in a secret store, not in source control or logs.

## Service account requirements

For a key to authenticate successfully, its service account must be both **active** and **not locked out**. A request bearing a key for a deactivated or locked-out account is treated as unauthenticated. Changes to a service account's status take effect on the next request.

## Authentication outcomes

| Condition | Outcome |
|---|---|
| The header is absent, empty, or its value does not match any active, non-locked service account. | The request is rejected with `401 Unauthorized`. |
| The header value matches an active, non-locked service account. | The request is authenticated as that service account and proceeds. |

## Rotating your API key

Service accounts can rotate their own API key without operator involvement. Send an authenticated request to the rotation endpoint and Fusion will generate a fresh key, replace the existing one, and return the new value in the response.

| Field | Value |
|---|---|
| Method | `POST` |
| URL | `https://fusion.globetrotter.com.au/api/v2/service-accounts/me/rotate-api-key` |
| Headers | `X-API-Key: <your current key>` |
| Request body | None |

The target service account is determined entirely from the `X-API-Key` header - there is no body, no path parameter, and no query parameter. You can only rotate your own key.

### Response

A successful rotation returns `200 OK` with a JSON body:

```json
{
  "id": "f3c5d8a4-7e2b-4c91-b6f0-1d2e3a4b5c6d",
  "name": "Acme Corp – production",
  "apiKey": "qf3p1c0Lz5y8Wn6Tj2Rh7Vk4Mb9Sg1Xa8Dc7Fe2Nq5Up0Ji3Yo6Lr4Hd9Bv1Sx2A==",
  "rotatedAt": "2026-05-16T14:32:07+10:00"
}
```

The `apiKey` field carries the **new** plaintext key (64 characters, base64-encoded from 48 bytes of cryptographic randomness).

{: .warning }

The new key is returned **once**. Fusion stores it but will not reveal it again on any subsequent request. Capture the value into your secret store before discarding the response.

### Effect on in-flight and subsequent requests

The current request completes normally - authentication is evaluated before the rotation runs - but the old key stops working the instant the rotation is persisted. Every request that follows must carry the new key in `X-API-Key`. If multiple workers share a key, coordinate the rollover so all of them pick up the new value before the next call.

### Failure modes

| Condition | Outcome |
|---|---|
| `X-API-Key` is absent, empty, or does not match an active, non-locked service account. | `401 Unauthorized`. No rotation occurs. |
| The key matched but the service account was removed between authentication and rotation. | `404 Not Found`. No rotation occurs; the old key remains valid until the account is recreated or another rotation is attempted. |
