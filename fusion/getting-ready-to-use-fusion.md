---
layout: default
title: Getting ready to use Fusion
parent: Fusion
nav_order: 10
permalink: /fusion/getting-ready-to-use-fusion/
---

Fusion listens on `https://fusion.globetrotter.com.au` port `443` and the API is accessed through `/api/<version>/<endpoint>`

Unencrypted requests (i.e. `http`/port `80`) are not supported.

A well-formed URI looks like this

````url
https://fusion.globetrotter.com.au/api/v1/invoices?query=totalAmount^GT1000;department^EQOperations;recordLocator^CTRX0
````

# Transport Layer Security

Fusion uses TLS 1.3 with fallback to TLS 1.2 for all API communication. Unencrypted transactions are not supported.

Client systems should have certificate issuer verification enabled and verify that the certificate is issued to `fusion.globetrotter.com.au`. The server's certificate fingerprint is available from the IT team upon request.

> **⚠ Never send your Credential information in cleartext**

# Organisational scope

All information that Globetrotter stores for a particular accounting entity (i.e. your client number) is available on Fusion when a valid credential is supplied. Potentially sensitive information, such as financial data for traveller, cost centre or department, is returned upon request. Technical integration teams should take care to implement appropriate segregation of client data inside the client system, as well as ensuring that only appropriate staff have access to Fusion.

> **⚠ Your credential gives unfettered access to ALL information stored by Globetrotter for your organisation**

Your CRM can advise if separate accounting entities are set up for your organisation.

# Versioning

The latest supported version is v1, for example:

````url
GET https://fusion.globetrotter.com.au/api/v1/invoices
````

Versioning the API allows continual improvement of Fusion without introducing breaking changes. The previous version of a particular endpoint is supported for 12 months from the release of the next version of that endpoint, or 60 days after the last access of the previous version, whichever comes first. After this time, the endpoint is removed.

# Cache and refresh

Fusion's cache refreshes every ten minutes. If you receive a `503 Temporarily Unavailable` error message it means that Fusion's cache is not ready to serve your records yet. Please try again in a few minutes.

Fusion will return transactional data with date ranges plus/minus 365 days from the current date. If information outside of this date range is required, you may be able to get it from Globetrotter Insight, or alternatively by contacting your CRM for a custom search. A fee applies.

# Accessing endpoints

Fusion listens on `https://fusion.globetrotter.com.au` port `443` and the API is accessed through `/api/<version>/<endpoint>`

Unencrypted requests (i.e. `http`/port `80`) are not supported.

A well-formed URI looks like this

````url
https://fusion.globetrotter.com.au/api/v1/invoices?query=totalAmount^GT1000;department^EQOperations;recordLocator^CTRX0
````

To access an informational endpoint, carry out the following steps:

1. Create a GET query
2. Add your `clientId` header (see the section on [authentication](#authentication) )
3. Add your `psk` header
4. Determine the endpoint and version
5. Add an optional `query` argument
6. Set the `Content-Type` and `Accept` headers to `application/json`
7. Send the query and listen for a response.

## Endpoint responses

Fusion sends responses in JSON format.

Each document is prefixed by a result key that contains an array of objects representing the records returned:

````json
{
  "result":
            [
              {...
````

To access individual results, deserialise the JSON and use `result[n].key...`

See the later sections of this guide for the complete endpoint documentation.

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

Globetrotter provides eight `standard fields` for client custom data to be stored with each booking. The standard fields are:

|Field|Data Type|Intended Purpose|
|---|---|---|
|department|string (30)|The department the booking is billed to|
|employeeId|string (50)|Traveller's client employee number or client unique identifier|
|costCentre|string (50)|The cost centre against which this booking is charged|
|purchaseOrder|string (50)|The purchase order for the booking|
|bookedBy|string (50)|The person who booked this travel|
|authorisedBy|string (50)|The person who authorised this travel|
|customData1|string (50)|Client-specified custom data field 1|
|customData2|string (50)|Client-specified custom data field 2|

These standard fields capture information that most clients want to record against their bookings. However, it is important to note that organisations do not have to use the fields for their intended purpose. Because all `standard fields` store `String` data, your organisation may use any of these fields for a different purpose. Speak to your CRM for details.

`customData1` and `customData2` are provided as unspecified-purpose fields for client convenience.

<!-- Styled Footer Navigation for Setup.md -->
<div style="display: flex; justify-content: space-between; width:95%; padding: 10px; background-color: #f8f8f8; border: 1px solid #ddd; border-radius: 5px; margin: 20px auto 0 auto;">
  <div style="text-align:left;"><a href="index.html" style="text-decoration:none; color:#007acc;">Home</a></div>
  <div style="text-align:center;"><a href="index.html" style="text-decoration:none; color:#007acc;">Home</a></div>
  <div style="text-align:right;"><a href="CertificateAuthentication.html" style="text-decoration:none; color:#007acc;">Certificate Authentication &rarr;</a></div>
</div>
