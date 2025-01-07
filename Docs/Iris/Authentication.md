# Authentication

Iris supports two authentication schemes:

* Username and password
* SSL certificate

The username and password scheme is used for normal user accounts, as used by people accessing Iris. SSL certificate authentication is designed for client systems to integrate with Iris.

## In this section

* [Username and password](#username-and-password)
    * [Web access](#web-access)
    * [API access](#api-access)
        * [Intial authentication](#intial-authentication)
        * [JWT for subsequent requests](#jwt-for-subsequent-requests)
* [SSL certificate](#ssl-certificate)
    * [Web access](#web-access)
    * [API access](#api-access)


## Username and password

### Web access

To access the Iris web application using a username and password, navigate to:

```url
https://{tenantDomain}.iris.globetrotter.com.au/login
```

* {`tenantDomain`} is the domain that identifies your tenancy.

Type your username and password and click `Login`. Upon successful authentication you will be redirected to the home page.

### API access

The Iris API can also be directly accessed with a username and password.

#### Intial authentication

 In your client, use the URL

```url
https://iris.globetrotter.com.au/tenant/{tenantId}/api/v1/authentication/login
```

* {tenantId} the UUID that represents your tenancy.

Construct a POST body in the following manner:

```json
{
    "email": <string>,
    "password": <string>
}
```

* {`email`}: The email address of the user
* {`password`}: The user's password.

Send the payload. If the username and password match an existing user on the tenancy, a 200 response will be sent with this body:

```json
{
    "result": {
        "jsonWebToken": <string>,
        "refreshToken": <string>
    }
}
```

* {jsonWebToken}: A base64 encoded JSON web token (JWT) that must be presented for subsequent operations. It is valid for 24 hours after issuing.
* {refreshToken}: A UUID that enables fast re-issuing of the JWT.

#### JWT for subsequent requests

The JWT must be sent to Iris for subsequent operations. In your client, set the `Authentication Type` to `Bearer Token`. Set the `token value` to the value of `result.jsonWebToken`. Construct the rest of your request and send. Iris will inspect the JWT for validity and respond to the request.

## SSL certificate

For client system integration, an SSL certificate is more appropriate. SSL certificates can be set to a standard validity period and rolled over at regular intervals to ensure security is maintained. Iris supports the use of SSL certificates for service accounts. SSL certificates cannot be used for normal user accounts (which must use a username and password).

### Web access

The web application cannot be accessed via a service account. Use a normal account for access to the web application.

### API access

Service accounts can make requests directly to Iris without authenticating first. Service accounts present their certificates for re-authentication as part of every request. Iris checks for certificate validity and will reject certificates with issues such as lapsed expiry or chain of authority problems.

To make a request in your client configure the client to present the security certificate as part of the request, and then make the request to the required URL directly.

Iris will compare the fingerprint of the certificate to the list of known service accounts for the tenancy, and if a fingerprint match is found will mark the incoming request as authenticated and allow access in line with the service account's permissions.
