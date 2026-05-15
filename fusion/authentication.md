---
layout: default
title: Authentication
parent: Globetrotter Fusion
nav_order: 20
permalink: /fusion/authentication/
---

# Authentication
{: .no_toc }

Fusion authenticates callers using an API key carried in the `X-API-Key` HTTP request header. Each key is bound to a **service account** — a named identity managed within Fusion — and confers the access granted to that account.

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

API keys are credentials. Fusion must only be accessed over HTTPS, and keys must be stored in a secret store, not in source control or logs.

## Service account requirements

For a key to authenticate successfully, its service account must be both **active** and **not locked out**. A request bearing a key for a deactivated or locked-out account is treated as unauthenticated. Changes to a service account's status take effect on the next request.

## Authentication outcomes

| Condition | Outcome |
|---|---|
| The header is absent, empty, or its value does not match any active, non-locked service account. | The request is rejected with `401 Unauthorized`. |
| The header value matches an active, non-locked service account. | The request is authenticated as that service account and proceeds. |