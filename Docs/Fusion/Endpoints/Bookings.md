# Bookings endpoint

The `bookings` endpoint contains the trip file data for a single passenger name record (PNR). Each booking/PNR is uniquely identified by the `recordLocator` which is a 6 character alphanumeric string.

## Return order

Bookings are returned in `bookingDate` ascending order.

## Warnings and considerations

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
