# Traveller Location endpoint

The `traveller location` endpoint is designed to help you determine the possible locations of your travellers based on their planned itinerary segments.

The traveller location endpoints provide location data for travel segments that have been booked directly in Globetrotter's GDS, or that have been added in to an existing GDS booking ('passives'). The traveller location can only return data that has been booked by Globetrotter. It does not track the current whereabouts of travellers in realtime.

This endpoint will return raw segment data that allows querying on departure dates/times, arrival dates/times, traveller, record locator and other fields.

## Return order

Segments are returned in `departureDate` ascending order.
 
## Warnings and considerations

> **⚠ Travel that has not been booked by Globetrotter or in a Globetrotter system (e.g. Serko or Concur) will not be returned.**

> **⚠ Location is based on planned segments and does not account for real-time updates such as cancellations, delays, or unscheduled stops.**

> **⚠ This endpoint does not track travellers in realtime.**

> **⚠ Some segments may not have latitude and longitude data available.**

> **⚠ This endpoint should not be relied upon as the sole data provider for on-trip traveller safety. Globetrotter recommends clients use ISOS.**

> **⚠ Dates and times listed are in UTC**
