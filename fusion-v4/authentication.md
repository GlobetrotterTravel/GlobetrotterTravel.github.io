---
layout: default
title: Authentication
parent: Globetrotter Fusion 4
nav_order: 20
permalink: /fusion-v4/authentication/
---

# Authentication
{: .no_toc }

Fusion authenticates client systems by an API key carried in the `X-API-Key` HTTP request header. Each key belongs to a **service account**: a named identity that Globetrotter sets up for one of your systems, linked to your organisation's client numbers.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## The X-API-Key header

Set the header on every request:

| Header | Value |
|---|---|
| `X-API-Key` | The API key issued to your service account. It begins with `f4_`. |

The value must match the issued key exactly. Only one key may be presented per request.

````bash
curl -H "X-API-Key: f4_..." https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net/api/v4/bookings
````

{: .warning }

API keys are credentials. Only send them over HTTPS, and keep them in a secret store, never in source control, logs, URLs or emails.

## Service accounts

Globetrotter creates a service account for each of your systems that calls Fusion, for example "Acme finance integration". Each service account has:

- **A name**, which identifies the system in Globetrotter's records.
- **Client numbers**: the Globetrotter client numbers whose information the key can see. See [Organisational scope](/fusion-v4/getting-ready-to-use-fusion/#organisational-scope).
- **An API key**, which Globetrotter gives you once, when the account is created. You can [rotate it](#rotating-your-api-key) yourself.
- **A status**. While an account is inactive, its key is refused.

Ask for a separate service account for each system, rather than sharing one key between them. A key can then be replaced, or a system's access withdrawn, without affecting your other systems.

## Testing your API key

Check that your key works, and which client numbers it is linked to, with `GET /api/v4/whoami`:

````bash
curl -H "X-API-Key: f4_..." https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net/api/v4/whoami
````

````powershell
Invoke-RestMethod https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net/api/v4/whoami -Headers @{ 'X-API-Key' = 'f4_...' }
````

A working key returns `200 OK` with the service account it belongs to:

````json
{
  "name": "Acme finance integration",
  "authenticationMethod": "ApiKey",
  "serviceAccountId": "0199a3c2-5b1e-7f40-9d2a-3c4b5d6e7f80",
  "clientNumbers": ["ACME01", "ACME02"],
  "roles": ["client-access"]
}
````

| Key | Meaning |
|---|---|
| `name` | The service account's name. |
| `authenticationMethod` | Always `ApiKey` for a client system. |
| `serviceAccountId` | The service account's identifier. Quote it when you contact Globetrotter about the account. |
| `clientNumbers` | The client numbers the key can see. |
| `roles` | The role that decides what the key may do. A client system that calls endpoints has `client-access`. |

The same response is also at `GET /api/v4/auth/me`.

## Authentication outcomes

| Condition | Outcome |
|---|---|
| The header holds a current key for an active service account, whose role allows the request. | The request is authenticated as that service account and proceeds. |
| The header is absent or empty, or its value isn't a current key for an active service account. | `401 Unauthorized`. |
| The key is current, but its service account's role doesn't allow the request. | `403 Forbidden`, saying which permission the request needs. |

A `401` response doesn't say why the key was refused, so it gives nothing away to someone guessing keys. A key is refused when it is wrong, has been replaced, or belongs to a service account that is inactive or has been deleted. If you get `401` with a key you believe is current, contact Globetrotter with your service account's name or ID. If you get `403`, the key works but its role doesn't cover the request: ask Globetrotter to check the account's role.

{: .note }

Some endpoints are public: they answer without an API key. They are marked in the [API specification](/fusion-v4/api-specification/) as having no security requirement. You may still send your key to a public endpoint, and some return more when you do.

## Rotating your API key

You can replace your API key yourself, whenever you choose, by calling `POST /api/v4/auth/rotate-key` with the key you want to replace. Rotate it:

- when your security policy requires keys to be changed, for example every 90 days
- when a person who knew the key leaves your organisation
- at once if the key may have been exposed, for example committed to source control, logged or sent by email.

````bash
curl -X POST -H "X-API-Key: f4_..." \
  https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net/api/v4/auth/rotate-key
````

````powershell
Invoke-RestMethod https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net/api/v4/auth/rotate-key `
  -Method Post -Headers @{ 'X-API-Key' = 'f4_...' }
````

A successful rotation returns `200 OK` with the new key:

````json
{
  "serviceAccount": {
    "id": "0199a3c2-5b1e-7f40-9d2a-3c4b5d6e7f80",
    "name": "Acme finance integration",
    "clientNumbers": ["ACME01", "ACME02"],
    "apiKeyHint": "f4_Q7xLm",
    "isActive": true,
    "createdAt": "2026-10-01T02:15:00+00:00",
    "updatedAt": "2026-10-13T08:30:00+00:00",
    "roleId": "client-access"
  },
  "apiKey": "f4_Q7xLm..."
}
````

| Key | Meaning |
|---|---|
| `apiKey` | The new key. It is shown only in this response. |
| `serviceAccount` | Your service account, unchanged apart from `apiKeyHint`, the new key's first 8 characters. |

{: .warning }

The old key stops working the moment the new key is issued. Any system still sending the old key gets `401 Unauthorized` from then on.

### Rotating without an outage

Because the old key stops working at once, plan the change:

1. Rotate from the system that will use the new key, or from a script that writes the new key straight into your secret store. Don't rotate from a place where the response could be lost.
2. Store the new key in your secret store before doing anything else. Globetrotter keeps only a fingerprint of it and can't retrieve it, so if the response is lost the key has to be rotated again, by Globetrotter, since the old key no longer works.
3. Check the new key with [`whoami`](#testing-your-api-key).
4. Update every system that shares the key. This is easiest when each system has [its own service account](#service-accounts): then only one system changes.

If a rotation request fails with a network error, you can't tell whether it took effect. Try the old key with `whoami`: if it still works, the rotation didn't happen and you can try again; if it gets `401`, contact Globetrotter for a new key.

A key can only rotate itself. Rotation keeps your service account's name, client numbers and access unchanged.

### When Globetrotter replaces your key

Contact the Globetrotter IT team to replace a key you can't rotate yourself, for example because it is lost, or because a rotation's response was lost. Globetrotter can also deactivate a key straight away if it has been exposed and you can't rotate it at once. The new key is given to you once, and the old key stops working immediately, so agree a time with Globetrotter for the change.
