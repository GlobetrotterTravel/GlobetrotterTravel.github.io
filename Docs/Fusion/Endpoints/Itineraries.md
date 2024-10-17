# Itineraries endpoint

The `itineraries` endpoint provides one or more machine readable itineraries including PNR information and subsequent segments.

## Ordering

Itineraries are returned in `bookingDate` ascending order.

## Warnings and considerations

> **🛈** This endpoint will return commercial air travel, car rental and hotel stay segments only. No other segment types are returned.

> **🛈** Globetrotter's policy is to place each traveller on a separate PNR, even when more than one person is travelling together.
 
> **🛈** Custom data is at the PNR/trip level, not at segment level.

> **⚠ Travel that has not been booked by Globetrotter or in a Globetrotter system (e.g. Serko or Concur) will not be returned.**

> **⚠ This endpoint does not track travellers in realtime.**

> **⚠ This endpoint should not be relied upon as the sole data provider for on-trip traveller safety. Globetrotter recommends clients use ISOS.**
