---
layout: default
title: Calling endpoints
parent: Globetrotter Fusion 4
nav_order: 30
permalink: /fusion-v4/calling-endpoints/
---

# Calling endpoints
{: .no_toc }

Each Fusion endpoint is a URL under `/api/v4/` that accepts one or more HTTP methods. This page describes what Fusion expects in a request, and what it sends back.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Endpoint URLs

An endpoint's URL is `/api/v4/<name>`, for example:

````url
https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net/api/v4/bookings
````

- Names contain letters, digits, hyphens and underscores, and are matched without regard to case: `/api/v4/Bookings` is the same endpoint as `/api/v4/bookings`.
- One name can accept several methods, e.g. `GET` to read and `POST` to add. Calling a name with a method it doesn't accept returns `405 Method Not Allowed`, with an `Allow` header listing the methods it does accept.
- The [API specification](/fusion-v4/api-specification/) lists every endpoint and method.

## Requests

### Methods

| Method | Typically used to |
|---|---|
| `GET` | Read information. Accepts the [query parameters](/fusion-v4/querying/) for filtering, sorting and paging. |
| `POST` | Send new information, or ask for something to be done. |
| `PUT` | Replace information. |
| `PATCH` | Change part of some information. |
| `DELETE` | Remove information. |

Each endpoint's summary in the [API specification](/fusion-v4/api-specification/) says what it does.

### Query strings

Endpoints may accept their own query string parameters, such as `?client=ACME01`. Globetrotter tells you which, and the endpoint's summary in the API specification may describe them. Repeat a parameter only if the endpoint accepts a list.

The parameter names `filter`, `orderby`, `page` and `pageSize`, with or without a leading `$`, are used by [querying](/fusion-v4/querying/): Fusion applies them to the endpoint's response. Don't use them for anything else.

### Request bodies

Send request bodies as JSON, with the `Content-Type: application/json` header:

````bash
curl -X POST -H "X-API-Key: f4_..." -H "Content-Type: application/json" \
  -d '{ "invoiceNumber": "INV-1001", "amount": 250.00 }' \
  https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net/api/v4/invoice-approvals
````

- A body sent as JSON that isn't valid JSON is refused with `400 Bad Request`.
- A body sent without a JSON content type is passed to the endpoint as plain text, which most endpoints don't accept.
- Request bodies are limited to 30,000,000 bytes (about 28.6 MB).

### Request schemas

Many endpoints that accept a body publish a **request schema**: a [JSON Schema](https://json-schema.org) that describes the body, in the [API specification](/fusion-v4/api-specification/). Fusion checks every request's body against the endpoint's request schema before it does anything else, so a request that doesn't match changes nothing.

| Request | Outcome |
|---|---|
| The body matches the schema. | The request proceeds. |
| The body doesn't match the schema. | `400 Bad Request`, listing each problem. |
| There is no body. | `400 Bad Request`: `A JSON request body is required`. |
| The body isn't sent as `application/json`. | `415 Unsupported Media Type`. |

A body that doesn't match lists each problem, up to 50, in `errors`:

````json
{
  "error": "The request body does not match the request schema",
  "endpoint": "/api/v4/invoice-approvals",
  "errors": [
    { "path": "/amount", "keyword": "type", "message": "type at /amount" },
    { "path": "/invoiceNumber", "keyword": "required", "message": "required at /invoiceNumber" }
  ]
}
````

| Key | Meaning |
|---|---|
| `path` | Where the problem is, as a JSON pointer: `/invoiceNumber` is the `invoiceNumber` key, `/lines/0/amount` is the `amount` of the first item in `lines`. Empty for the body as a whole. |
| `keyword` | The JSON Schema keyword the body fails, e.g. `required` (a required key is missing), `type` (a value is the wrong type), `pattern` (a value isn't in the required format). |
| `message` | The keyword and path together, for logs. |

{: .note }

Validate request bodies against the request schema in your own system too, before sending them. Most JSON Schema libraries can, and many code generators do it for you from the API specification.

## Responses

### Content types

| Content type | Body |
|---|---|
| `application/json` | JSON. Most endpoints respond this way. |
| `text/html` | An HTML page, for endpoints designed to be opened in a browser. Scripts are removed, and the response forbids running any. |
| Others, e.g. `text/csv` or `text/plain` | As described by the endpoint. |

Some endpoints respond with `302 Found` and a `Location` header, to send a browser on to another URL.

A `200 OK` JSON response may be filtered, sorted and paged by the [query parameters](/fusion-v4/querying/). An endpoint may also respond with another success code, such as `201 Created` or `204 No Content` (no body).

### Response schemas

Many endpoints publish a **response schema** in the [API specification](/fusion-v4/api-specification/): a JSON Schema describing a `200 OK` response. Use it to generate the types your system reads responses into.

A response schema describes the response as Globetrotter designed it. Write your system to ignore keys it doesn't recognise, so that new keys added to a response don't break it.

## Errors

Errors are returned as JSON with an `error` key describing the problem, and often the `endpoint` that was called:

````json
{
  "error": "The request body does not match the request schema",
  "endpoint": "/api/v4/invoice-approvals"
}
````

| Status | Meaning | What to do |
|---|---|---|
| `400 Bad Request` | The request is invalid: the body isn't valid JSON, doesn't match the request schema or is missing, or a [query parameter](/fusion-v4/querying/#errors) is invalid. Endpoints also return `400` for requests they can't act on, such as a missing parameter. | Correct the request. The `error` (and `errors`, for a request schema) says what is wrong. |
| `401 Unauthorized` | No API key was sent, or it isn't a current key for an active service account. | Check your key with [`whoami`](/fusion-v4/authentication/#testing-your-api-key). |
| `404 Not Found` | There is no endpoint at that URL, or it isn't available. | Check the URL against the [API specification](/fusion-v4/api-specification/). |
| `405 Method Not Allowed` | The endpoint doesn't accept that method. | Use a method listed in the `Allow` header. |
| `415 Unsupported Media Type` | The endpoint has a request schema, and the body wasn't sent as JSON. | Send the body with `Content-Type: application/json`. |
| `500 Internal Server Error` | The endpoint failed while handling the request. | Retry later. If it persists, contact Globetrotter with the time of the request and the `error`. |
| `503 Service Unavailable` | Something the endpoint needs, usually the data warehouse, is resuming after a quiet period. | Retry after the number of seconds in the `Retry-After` header. It can take about a minute. |
| `507 Insufficient Storage` | A non-JSON response was larger than Fusion sends (5,000,000 characters). | Ask for less, e.g. a narrower date range. |

Endpoints can return other status codes of their own. Treat any other `4xx` as a problem with the request, and any other `5xx` as a failure to retry later.

{: .note }

Error messages are written for people. Base your system's decisions on the status code, not on the text of `error`, which may change.
