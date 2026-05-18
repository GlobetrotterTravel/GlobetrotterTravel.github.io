---
layout: default
title: Getting ready to use Fusion
parent: Globetrotter Fusion
nav_order: 10
permalink: /fusion/getting-ready-to-use-fusion/
---

# Getting ready to use Fusion
{: .no_toc }

Fusion listens on `https://fusion.globetrotter.com.au` port `443` and the API is accessed through `/api/<version>/<endpoint>`

Unencrypted requests (i.e. `http`/port `80`) are not supported.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Transport Layer Security

Fusion uses TLS 1.3 with fallback to TLS 1.2 for all API communication. Unencrypted transactions are not supported.

Client systems should have certificate issuer verification enabled and verify that the certificate is issued to `fusion.globetrotter.com.au`. The server's certificate fingerprint is available from the IT team upon request.

{: .warning }

Never send your Credential information in cleartext

## Organisational scope

All information that Globetrotter stores for a particular accounting entity (i.e. your client number) is available on Fusion when a valid credential is supplied. Potentially sensitive information, such as financial data for traveller, cost centre or department, is returned upon request. Technical integration teams should take care to implement appropriate segregation of client data inside the client system, as well as ensuring that only appropriate staff have access to Fusion.

{: .warning }

Your credential gives unfettered access to ALL information stored by Globetrotter for your organisation

Your CRM can advise if separate accounting entities are set up for your organisation.

## Versioning

The supported API version is v2, for example:

````url
GET https://fusion.globetrotter.com.au/api/v2/invoices
````

Versioning lets us improve Fusion without introducing breaking changes. When we publish a new version of an endpoint, the previous version remains available for at least 12 months - or until the previous version has not been accessed for 60 days, whichever comes first. After that, the older version is withdrawn. We may also withdraw endpoints that have not been meaningfully used for 60 days.

## Cache and refresh

Fusion's cache refreshes periodically. If you receive a `503 Temporarily Unavailable` error message it means that Fusion's cache is not ready to serve your records yet. Please try again in a few minutes.

{: .warning }

Fusion's data is refreshed multiple times per day on schedule. The data is not real-time. Fusion's dataset may lag source systems by a few hours at any one time.

Fusion will return transactional data with date ranges plus/minus 365 days from the current date (depending on the type of record). If information outside of this date range is required, you may be able to get it from Globetrotter Insight, or alternatively by contacting your CRM for a custom search. A fee may apply.

## Accessing endpoints

To access an informational endpoint, carry out the following steps:

1. Create a GET query
2. Add your `X-API-Key` header (see [Authentication](/fusion/authentication/))
3. Determine the endpoint and version
4. Add any optional filters, sorting and pagination (see [Querying](/fusion/querying/))
5. Set the `Content-Type` and `Accept` headers to `application/json`
6. Send the query and listen for a response.

## Endpoint responses

Fusion sends responses in JSON format.

To access individual results, deserialise the JSON and step through the array of objects.

Dates returned by Fusion are formatted in ISO8601 format: **YYYY-MM-DD** while times are formatted **hh:mm**. Datetimes are formatted **YYYY-MM-DDThh:mm**. Dates and times are in the time zone specified.

Boolean values are specified as `true` or `false` (not 1 or 0). Boolean key names are prepended by `is` or `has`, for example

````json
"isTaxable"
````

````json
"hasFlown"
````

Key names are standardised across endpoints and use *camelCase*, although both the query engine and endpoint router are case-insensitive.

Key names are chosen to be readable and intuitive. If the purpose of a key can be inferred from the name then a single word is used (e.g. `department`), and common words have their purpose prepended. Additionally, logical groups of keys are named alike for readability.

Examples:

````json
"department"
````

as opposed to

```json
"departmentName"
````

and

````json
"invoiceNumber"
````

as opposed to

````json
"number"
````

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

These standard fields capture information that most clients want to record against their bookings. However, it is important to note that clients do not have to use the fields for their intended purpose. Because all `standard fields` store `String` data, your organisation may use any of these fields for a different purpose. Speak to your CRM for details.
