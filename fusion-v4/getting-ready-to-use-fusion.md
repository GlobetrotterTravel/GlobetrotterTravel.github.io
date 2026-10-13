---
layout: default
title: Getting ready to use Fusion
parent: Globetrotter Fusion 4
nav_order: 10
permalink: /fusion-v4/getting-ready-to-use-fusion/
---

# Getting ready to use Fusion
{: .no_toc }

Fusion listens on `https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net` port `443`, and the API is accessed through `/api/v4/<endpoint>`.

Unencrypted requests (i.e. `http`/port `80`) are not supported.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Transport Layer Security

All API communication is encrypted with TLS 1.2 or later. Unencrypted transactions are not supported.

Client systems should have certificate verification enabled, and verify that the certificate is valid for the host name above.

{: .warning }

Never send your API key in cleartext.

## Versioning

The current API version is v4. Every endpoint is under `/api/v4/`, for example:

````url
GET https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net/api/v4/bookings
````

A request to a path that isn't an endpoint, including a path under another version, returns `404 Not Found`.

{: .note }

The endpoint names in this guide, such as `bookings`, are examples. Globetrotter sets up the endpoints you use and tells you their names. The [API specification](/fusion-v4/api-specification/) lists every endpoint that is available.

## Organisational scope

Your API key is linked to one or more Globetrotter client numbers: the accounting entities set up for your organisation. Endpoints use those client numbers to decide what to return, so a key only ever returns information for its own client numbers. You don't send client numbers in your requests.

Potentially sensitive information, such as financial data for a traveller, cost centre or department, is returned upon request. Technical integration teams should take care to implement appropriate segregation of client data inside the client system, and to ensure that only appropriate staff have access to Fusion and its API keys.

{: .warning }

Your API key gives access to ALL information that Fusion's endpoints return for your client numbers.

Your CRM can advise which client numbers are set up for your organisation. You can see which ones your key is linked to with the `whoami` endpoint (see [Authentication](/fusion-v4/authentication/#testing-your-api-key)).

## Data freshness

Fusion reads most of its information from Globetrotter's data warehouse, which is refreshed from Globetrotter's booking and accounting systems on a schedule during the day.

{: .warning }

Fusion's data is not real-time. It may lag the source systems by a few hours at any one time.

## Response times and timeouts

Most requests are answered in under a few seconds, but some endpoints do more work and take longer.

After a quiet period, the data warehouse pauses, and takes about a minute to resume. A request that needs it while it resumes isn't kept waiting: Fusion answers at once with `503 Service Unavailable` and a `Retry-After` header, usually 15 seconds, and the request succeeds once the warehouse is back.

To allow for this:

- Set your client's request timeout to at least 2 minutes.
- If a request fails with `503`, retry it after the number of seconds in its `Retry-After` header. Expect a few retries over about a minute.
- If a request times out, or fails with another `5xx` status code, retry it after a short wait, waiting longer after each attempt. Don't retry `4xx` responses without changing the request: they will fail again.
- Avoid retrying requests that change data (`POST`, `PUT`, `PATCH`, `DELETE`) unless the endpoint's documentation says that's safe.

## Accessing endpoints

To call an endpoint, carry out the following steps:

1. Determine the endpoint and its HTTP method (see the [API specification](/fusion-v4/api-specification/))
2. Add your `X-API-Key` header (see [Authentication](/fusion-v4/authentication/))
3. Set the `Accept` header to `application/json`
4. For a `GET` request, add any optional filters, sorting and pagination (see [Querying](/fusion-v4/querying/))
5. For a request with a body, set the `Content-Type` header to `application/json` and send the body as JSON
6. Send the request and handle the response (see [Calling endpoints](/fusion-v4/calling-endpoints/)).

For example:

````bash
curl -H "X-API-Key: f4_..." -H "Accept: application/json" \
  "https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net/api/v4/bookings?\$orderby=bookingDate%20desc"
````

````powershell
Invoke-RestMethod "https://fusion-v4-a3fpf5ccbyczhfc7.australiaeast-01.azurewebsites.net/api/v4/bookings?`$orderby=bookingDate desc" `
  -Headers @{ 'X-API-Key' = 'f4_...'; Accept = 'application/json' }
````

## Endpoint responses

Fusion sends responses in JSON format, unless an endpoint's documentation says otherwise.

To access individual results, deserialise the JSON and step through the array of objects.

Each endpoint's response shape, including its field names and types, is described in the [API specification](/fusion-v4/api-specification/). Fusion's endpoints follow these conventions:

- Dates and times are formatted in ISO 8601 format, e.g. **2026-10-07** or **2026-10-07T09:30:00**. The API specification gives the format of each field.
- Boolean values are `true` or `false` (not 1 or 0). Boolean key names begin with `is` or `has`, for example `isTaxable` or `hasFlown`.
- Key names use *camelCase*. They are chosen to be readable: if the purpose of a key can be inferred from a single word, a single word is used (e.g. `department`, as opposed to `departmentName`), and otherwise its purpose is included (e.g. `invoiceNumber`, as opposed to `number`).

## How Globetrotter stores custom data

Globetrotter provides eleven `standard fields` for client custom data to be stored with each booking. The standard fields are:

|Field|Data Type|Intended Purpose|
|---|---|---|
|department|string (50)|The department the booking is billed to|
|costCentre|string (50)|The cost centre against which this booking is charged|
|projectCode|string (50)|The project code against which this booking is charged|
|reasonForTravel|string (50)|The reason that the travel was requested|
|purchaseOrder|string (50)|The purchase order for the booking|
|coordinator|string (50)|The person who booked this travel|
|authoriser|string (50)|The person who authorised this travel|
|customData1|string (50)|Client-specified custom data field 1|
|customData2|string (50)|Client-specified custom data field 2|
|customText1|string (50)|Client-specified custom text field 1|
|customText2|string (50)|Client-specified custom text field 2|

These standard fields capture information that most clients want to record against their bookings. However, clients do not have to use the fields for their intended purpose. Because all `standard fields` store `String` data, your organisation may use any of these fields for a different purpose. Speak to your CRM for details.
