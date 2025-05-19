---
layout: default
title: Itineraries
parent: Endpoints
nav_order: 40
permalink: /fusion/endpoints/itineraries/
---

# Invoices endpoint
# Itineraries endpoint

The `itineraries` endpoint provides one or more machine readable itineraries including PNR information and subsequent segments.

## Return order

Itineraries are returned in `bookingDate` ascending order.

## Considerations

> **🛈** This endpoint will return commercial air travel, car rental and hotel stay segments only. No other segment types are returned.

> **🛈** Globetrotter's policy is to place each traveller on a separate PNR, even when more than one person is travelling together.
 
> **🛈** Custom data is at the PNR/trip level, not at segment level.

> **⚠ Travel that has not been booked by Globetrotter or in a Globetrotter system (e.g. Serko or Concur) will not be returned.**

> **⚠ This endpoint does not track travellers in realtime.**

> **⚠ This endpoint should not be relied upon as the sole data provider for on-trip traveller safety. Globetrotter recommends clients use ISOS.**

<!-- Styled Footer Navigation for endpoints -->
<div style="display: flex; justify-content: space-between; width:95%; padding: 10px; background-color: #f8f8f8; border: 1px solid #ddd; border-radius: 5px; margin: 20px auto 0 auto;">
  <div style="text-align:left;"></div>
  <div style="text-align:center;"><a href="../index.html" style="text-decoration:none; color:#007acc;">Home</a></div>
  <div style="text-align:right;"></div>
</div>
