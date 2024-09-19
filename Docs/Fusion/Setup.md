# Setup and accessing endpoints

Fusion listens on `https://fusion.globetrotter.com.au` port `443` and the API is accessed through `/api/<version>/<endpoint>`

Unencrypted requests (i.e. `http`/port `80`) are not supported.

A well-formed URI looks like this

````url
https://fusion.globetrotter.com.au/api/v1/invoices?query=totalAmount^GT1000;department^EQOperations;recordLocator^CTRX0
````

# Transport Layer Security

Fusion uses TLS 1.3 with fallback to TLS 1.2 for all API communication. Unencrypted transactions are not supported.

# Organisational scope

All information that Globetrotter stores for a particular accounting entity is available on Fusion when a valid credential is supplied. Potentially sensitive information, such as financial data for traveller, cost centre or department, is returned upon request. Technical integration teams should take care to implement appropriate segregation of client data inside the client system, as well as ensuring that only appropriate staff have access to Fusion.

> **⚠ Your credential gives unfettered access to ALL information stored by Globetrotter for your organisation**

Your CRM can advise if separate accounting entities are set up for your organisation.

# Versioning

The latest supported version is v1, for example:

````url
GET https://fusion.globetrotter.com.au/api/v1/invoices
````

Versioning the API allows continual improvement of Fusion without introducing breaking changes. The previous version of a particular endpoint is supported for 12 months from the release of the next version of that endpoint, or 60 days after the last access of the previous version, whichever comes first. After this time, the endpoint is removed.

# Cache and refresh

Fusion's cache refreshes every ten minutes. If you receive a `503 Temporarily Unavailable` error message it means that Fusion's cache is not ready to serve your records yet. Please try again in a few minutes.
