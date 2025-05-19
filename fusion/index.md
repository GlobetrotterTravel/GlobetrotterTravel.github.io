---
layout: default
title: Globetrotter Fusion
nav_order: 20
has_children: true
permalink: /fusion/
---

# Welcome to Globetrotter Fusion

Globetrotter Fusion is Globetrotter's client-facing REST API platform.

Use Globetrotter Fusion to easily query your data to gain even more insight into your travel programme. Access your information on-demand, 24 hours a day with no limitations.

With Globetrotter Fusion you can:

* Electronically check booking status and other information from your ERP without using flat files
* Automatically keep track of your travellers' locations
* Insert invoicing data directly into your general ledger without the need to receive individual invoices
* Use your own analytics platform to mashup your travel data with other data from your business operations
* Make workflow decisions based on real-time travel information
* Deliver your travel information to your third party partners as required.

Globetrotter Fusion is:

* **Fast**: The lightweight API transfers information immediately, doing away with old Excel files and slow emails
* **Secure**: Bank-level security, strong encryption and secure authentication protects your valuble data
* **Modern**: Fusion is designed to support REST API, using standardised data access methods that enable easy-integration with client systems
* **Reliable**: Fusion is written using modern languages and frameworks, ensuring reliability of operation
* **Simple**: Cut down development and turnaround time through the use of modern, supported data interchange formats.

The API is intended for use by technical system integrators who wish to connect their organisation's analytics, CRM, HR or operations platforms to Globetrotter. If you would prefer to see predefined charts and reports, you can access Globetrotter Insight instead. Contact your Client Relationship Manager (CRM) to get an account for Globetrotter Insight.

Globetrotter Fusion is copyright (c) 2020-2024 Globetrotter. All rights reserved.

## Document version

This guide was last updated in May 2025.

## Getting an account for Globetrotter Fusion

Your CRM can organise an account to access Globetrotter Fusion. Please contact them to begin a free trial account.

## Getting help

We've worked hard to make the Globetrotter Fusion integration experience as simple as possible. While this guide should answer most of your questions, the Globetrotter IT team is also available to assist you.

We can be contacted:

| | |
|---|---|
|By Phone|+61 9442 0150
|By Email|itsupport@globetrotter.com.au|

## Document conventions

The following conventions are used in this guide.

> **🛈 Notes are denoted by the information icon, and provide tips and reminders that assist with using Fusion.**

> **⚠ Warnings are denoted by the alert icon. These notices should be read carefully. Failure to follow warning advice can result in security issues, data compromise or data loss.**

Keys, fields, values and other items that are displayed on screen are denoted like `this`.

Examples and URLs are denoted in fixed width type

````html
like this.
````

The term *field* is used generally to refer to an individual piece of information, whereas *key* refers to a specific item within a JSON document.

## Endpoints

The following endpoints are available:

- [Bookings](/fusion/endpoints/bookings/): The `bookings` endpoint contains the trip file data for a single passenger name record (PNR). Each booking/PNR is uniquely identified by the `recordLocator` which is a 6 character alphanumeric string.
- [Itineraries](/fusion/endpoints/itineraries/): The `itineraries` endpoint provides one or more machine readable itineraries including PNR information and subsequent segments.
- [Invoices](/fusion/endpoints/invoices/): The `invoices` endpoint provides one or more machine readable invoices, including line item and payment information. These invoices can be imported directly into an ERP or accounting system to support seamless financial processes.
- [Unused Tickets](/fusion/endpoints/unused-tickets/): The `unusedtickets` endpoint provides the unused tickets (aka "tickets in credit") for the current client. 
- [Traveller Location](/fusion/endpoints/traveller-location/): The `traveller location` endpoint is designed to help you determine the possible locations of your travellers based on their planned itinerary segments.

## Jump to

[Getting ready to use Fusion](/fusion/getting-ready-to-use-fusion/)

[Certificate Authentication](/fusion/certificate-authentication/)

[Pre-Shared Key Authentication](/fusion/pre-shared-key-authentication/)

[The query engine](/fusion/the-query-engine/)

[Pagination](/fusion/pagination/)

[Fusion Swagger Documentation](/fusion/swagger/)
