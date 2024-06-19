# Traveller Location endpoint

The `traveller location` endpoint is designed to help you determine the current locations of your travellers based on their planned itineraries. This information is useful for safety checks, especially during natural disasters or other incidents.

The traveller location endpoints provide location data for travel segments that have been booked directly in Globetrotter's GDS, or that have been added in to an existing GDS booking ('passives'). The traveller location can only return data that has been booked by Globetrotter.

This endpoint can be queried for the current date and time, or historical and future dates and times can be supplied.

## How traveller location is determined

The traveller location is determined in the following method:

1. **Pre-Trip**:
   - If a traveller's first segment has not yet departed, the traveller is considered to be at the departure location of that first segment. Travel segments that begin more than 30 days in the future will not be returned.

2. **On-Trip**:
   - If a traveller is currently on a segment (i.e., the departure time of the segment has passed, but the arrival time has not yet occurred), the traveller is considered to be at both the departure and arrival locations of the current segment.
   - This means that during a segment's duration, the traveller will be listed as being in two locations simultaneously: the segment's departure location and its arrival location.

3. **Post-Trip**:
   - If a traveller has completed their trip (i.e., all segments in their itinerary have occurred), the traveller is considered to be at the arrival location of their last segment. Travel segments more than 30 days in the past will not be returned.
  
## Warnings and considerations

> **⚠ Travel that has not been booked by Globetrotter or in a Globetrotter system (e.g. Serko or Concur) will not be returned.**

> **⚠ Location is based on planned segments and does not account for real-time updates such as cancellations, delays, or unscheduled stops.**

> **⚠ It is possible for some travellers to be listed as being in two locations at the same time when they are on a segment. This is intentional and ensures that all potential locations are covered during the segment's duration.**

> **⚠ Some segments may not have latitude and longitude data available.**

> **⚠ This endpoint should not be relied upon as the sole data provider for on-trip traveller safety. Globetrotter recommends clients use ISOS.**

> **⚠ Dates and times listed are in UTC**
