# Unused Tickets endpoint

The `unusedtickets` endpoint provides the unused tickets (aka "tickets in credit") for the current client. 

Unused tickets come about from a flight that has been ticketed and then cancelled. Cancellations can either be "All Open" or "Partial".

## Return order

Unused tickets are returned in `expiryDate` ascending order.

## Considerations

> **🛈 An "All Open" unused ticket means the ticket can be used for the named traveller or name changed to another. Unused tickets marked "Partial" means that one of the segments on the ticket has been flown by the named traveller and can only be reissued to the named traveller.**
