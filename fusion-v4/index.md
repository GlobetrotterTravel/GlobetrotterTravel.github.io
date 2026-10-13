---
layout: default
title: Globetrotter Fusion 4
has_children: true
permalink: /fusion-v4/
---

# Globetrotter Fusion 4
{: .no_toc }

Globetrotter Fusion is Globetrotter's client-facing REST API platform.

Use Globetrotter Fusion to query your data and gain more insight into your travel programme. Access your information on demand, 24 hours a day.

With Globetrotter Fusion you can:

* Check booking status and other information from your ERP electronically, without flat files
* Insert invoicing data directly into your general ledger, without receiving individual invoices
* Use your own analytics platform to combine your travel data with data from your other business operations
* Make workflow decisions based on travel information
* Deliver your travel information to your third-party partners as required.

{: .warning }

Fusion is **not** a safety-critical system. It does not provide real-time data and should not be relied upon for traveller safety or other critical processes. Please speak to your CRM about options for traveller safety services.

Globetrotter Fusion is:

* **Fast**: The lightweight API transfers information immediately, doing away with Excel files and slow emails
* **Secure**: Encrypted connections and per-system API keys protect your valuable data
* **Modern**: Fusion is a REST API that uses standard data formats, so it is easy to integrate with client systems
* **Self-describing**: A live OpenAPI specification describes every endpoint, so you can generate client code and keep it up to date
* **Simple**: Cut down development and turnaround time through modern, widely supported data interchange formats.

The API is intended for technical system integrators who want to connect their organisation's analytics, CRM, HR or operations platforms to Globetrotter. If you would prefer predefined charts and reports, you can use Globetrotter Insight instead. Contact your Client Relationship Manager (CRM) to get an account for Globetrotter Insight.

Globetrotter Fusion is copyright (c) 2020-2026 Globetrotter. All rights reserved.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Document version

This guide describes Fusion API version 4 (`/api/v4/`). It was last updated in October 2026.

## Getting an account for Globetrotter Fusion

Your CRM can organise access to Globetrotter Fusion. Please contact them to begin a free trial.

Globetrotter will give you an API key for each of your systems that calls Fusion, and tell you which endpoints have been set up for you. See [Authentication](/fusion-v4/authentication/).

## Getting help

We've worked hard to make integrating with Globetrotter Fusion as simple as possible. While this guide should answer most of your questions, the Globetrotter IT team is also available to assist you.

We can be contacted:

| | |
|---|---|
|By Phone|+61 8 9442 0150|
|By Email|itsupport@globetrotter.com.au|

When you contact us about a request that didn't work, please include the time of the request, the endpoint's URL and method, the response status code and the response body. Never send us your API key.

## Document conventions

The following conventions are used in this guide.

{: .note }

Notes are denoted by the information icon, and provide tips and reminders that assist with using Fusion.

{: .warning }

Warnings are denoted by the alert icon. These notices should be read carefully. Failure to follow warning advice can result in security issues, data compromise or data loss.

Keys, fields, values and other items that are displayed on screen are denoted like `this`.

Examples and URLs are denoted in fixed width type

````html
like this.
````

The term *field* is used generally to refer to an individual piece of information, whereas *key* refers to a specific item within a JSON document. *API key* always means the credential described in [Authentication](/fusion-v4/authentication/).

## In this guide

- [Getting ready to use Fusion](/fusion-v4/getting-ready-to-use-fusion/): How Fusion is hosted and versioned, what your API key can see, and how fresh the data is.
- [Authentication](/fusion-v4/authentication/): Sending your API key in the `X-API-Key` header, and testing it.
- [Calling endpoints](/fusion-v4/calling-endpoints/): Requests, request bodies, responses and errors.
- [Querying](/fusion-v4/querying/): Filtering, sorting and paginating list-shaped responses.
- [API specification](/fusion-v4/api-specification/): The live OpenAPI document that describes every endpoint.

## API specification

Fusion publishes an OpenAPI 3.1 specification of its endpoints, generated from the endpoints as they are when you request it. Use it to generate client libraries, import into tools such as Postman or Insomnia, or browse the request and response schemas of every endpoint. See [API specification](/fusion-v4/api-specification/) for how to download it.
