---
layout: default
title: Endpoints
parent: Globetrotter Fusion
nav_order: 60
has_children: true
has_toc: false
permalink: /fusion/endpoints/
---

# Endpoints

The following endpoints are available:

- [Bookings](/fusion/endpoints/bookings/): The `bookings` endpoint contains the trip file data for a single passenger name record (PNR). Each booking/PNR is uniquely identified by the `recordLocator` which is a 6 character alphanumeric string.
- [Itineraries](/fusion/endpoints/itineraries/): The `itineraries` endpoint provides one or more machine readable itineraries including PNR information and subsequent segments.
- [Invoices](/fusion/endpoints/invoices/): The `invoices` endpoint provides one or more machine readable invoices, including line item and payment information. These invoices can be imported directly into an ERP or accounting system to support seamless financial processes.
- [Unused Tickets](/fusion/endpoints/unused-tickets/): The `unusedtickets` endpoint provides the unused tickets (aka "tickets in credit") for the current client. 
- [Traveller Location](/fusion/endpoints/traveller-location/): The `traveller location` endpoint is designed to help you determine the possible locations of your travellers based on their planned itinerary segments.