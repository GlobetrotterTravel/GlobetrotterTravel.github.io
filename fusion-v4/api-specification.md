---
layout: default
title: API specification
parent: Globetrotter Fusion 4
nav_order: 50
permalink: /fusion-v4/api-specification/
---

# API specification
{: .no_toc }

Fusion publishes an [OpenAPI 3.1](https://spec.openapis.org/oas/v3.1.2) specification of its endpoints. Fusion builds it from the endpoints as they are at the moment you request it, so it is always up to date.

Use it to:

- see which endpoints are available, and what each one accepts and returns
- generate a client library for your language
- import the endpoints into tools such as Postman or Insomnia
- check your integration still matches Fusion when an endpoint changes.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Downloading the specification

The specification is at `/openapi/dynamic.json`. It needs your API key, like any other endpoint:

````bash
curl -H "X-API-Key: f4_..." -o fusion-openapi.json \
  https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net/openapi/dynamic.json
````

````powershell
Invoke-WebRequest https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net/openapi/dynamic.json `
  -Headers @{ 'X-API-Key' = 'f4_...' } -OutFile fusion-openapi.json
````

{: .note }

The specification is under `/openapi/`, not `/api/v4/`.

## What it contains

The specification lists every endpoint that is available, under `paths`, one operation for each method an endpoint accepts. Each operation has:

| Part | Contents |
|---|---|
| `operationId` | The method and endpoint name, e.g. `get-bookings` or `post-invoice-approvals`. It stays the same while the endpoint keeps its name and method, so code generated from it keeps its method names. |
| `summary` | What the endpoint does. |
| `requestBody` | For `POST`, `PUT` and `PATCH`: the request schema, which Fusion [checks every request against](/fusion-v4/calling-endpoints/#request-schemas). When the endpoint has a request schema, the body is marked `required`. |
| `responses` | The response schema for `200`, and the errors the endpoint can return: `400`, `404`, `405` and `500` always, `401` and `403` when it needs an API key, and `415` when it has a request schema. An API key gets `403` when it's valid but its service account isn't allowed to call endpoints; ask Globetrotter to check the account's role. |
| `parameters` | For `GET`: the optional [query parameters](/fusion-v4/querying/) `$filter`, `$orderby`, `$page` and `$pageSize`. |
| `security` | `ApiKey` (the `X-API-Key` header) when the endpoint needs an API key; none when it is public. |

Where an endpoint has no request or response schema, the specification describes that body as any JSON value. Ask Globetrotter if you need a schema for one.

Any endpoint can also return two responses the specification doesn't list: `503 Service Unavailable`, with a `Retry-After` header, while the data warehouse resumes, and `507 Insufficient Storage` for a non-JSON response that is too large. See [Errors](/fusion-v4/calling-endpoints/#errors).

### Shared definitions

When a schema defines parts once and refers to them from several places, the specification moves those parts to `components/schemas`, named after the endpoint, e.g. `get-bookings-response-traveller`. The schema's references point there instead.

### Security schemes

The specification declares two security schemes. Use `ApiKey`: send your API key in the `X-API-Key` header. `Staff` is for Globetrotter staff, who sign in with their Microsoft account; client systems can't use it.

## Using the specification

### Base URL

Set your client's base URL to:

````url
https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net
````

The specification's `servers` entry is filled in from the request it was downloaded with. Set the base URL yourself rather than relying on it, and make sure it begins with `https://`.

### Generating a client

Most OpenAPI 3.1 tools can generate a client library from the specification, for example [Kiota](https://learn.microsoft.com/openapi/kiota/), [NSwag](https://github.com/RicoSuter/NSwag) and [OpenAPI Generator](https://openapi-generator.tech). Configure the generated client to:

- send your API key in the `X-API-Key` header on every request
- allow at least 2 minutes for a response (see [Response times and timeouts](/fusion-v4/getting-ready-to-use-fusion/#response-times-and-timeouts))
- ignore keys in responses that it doesn't recognise, so that new keys added to a response don't break it.

### Importing into Postman or Insomnia

Import the downloaded `fusion-openapi.json` file. Then set the `X-API-Key` header, or an API key authorisation, on the collection, so that every request sends it.

