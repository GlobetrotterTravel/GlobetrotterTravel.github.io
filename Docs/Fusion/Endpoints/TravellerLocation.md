# Traveller Location endpoint

The `traveller location` endpoint is designed to help you determine the possible locations of your travellers based on their planned itinerary segments.

The traveller location endpoints provide location data for travel segments that have been booked directly in Globetrotter's GDS, or that have been added in to an existing GDS booking ('passives'). The traveller location can only return data that has been booked by Globetrotter. It does not track the current whereabouts of travellers in realtime.

This endpoint will return raw segment data that allows querying on departure dates/times, arrival dates/times, traveller, record locator and other fields.

Segments will be returned if they have any part occurring within the 60-day window from 30 days ago to 30 days from now. This means:

- Segments with a departure date up to 30 days in the future are included.
- Segments with an arrival date up to 30 days in the past are included.
- Segments that are currently in progress, started within the last 30 days, or will start within the next 30 days are included.

## Return order

Segments are returned in `departureDate` ascending order.
 
## Considerations

> **🛈** Dates and times listed are in UTC.

> **🛈** This endpoint will return commercial air travel, car rental and hotel stay segments only. No other segment types are returned.

> **🛈** Globetrotter's policy is to place each traveller on a separate PNR, even when more than one person is travelling together.

> **⚠ Travel that has not been booked by Globetrotter or in a Globetrotter system (e.g. Serko or Concur) will not be returned.**

> **⚠ Location is based on planned segments and does not account for real-time updates such as cancellations, delays, or unscheduled stops.**

> **⚠ This endpoint does not track travellers in realtime.**

> **⚠ Some segments may not have latitude and longitude data available.**

> **⚠ This endpoint should not be relied upon as the sole data provider for on-trip traveller safety. Globetrotter recommends clients use ISOS.**

<!-- Styled Footer Navigation for endpoints -->
<div style="display: flex; justify-content: space-between; width:95%; padding: 10px; background-color: #f8f8f8; border: 1px solid #ddd; border-radius: 5px; margin: 20px auto 0 auto;">
  <div style="text-align:left;"></div>
  <div style="text-align:center;"><a href="../index.html" style="text-decoration:none; color:#007acc;">Home</a></div>
  <div style="text-align:right;"></div>
</div>
