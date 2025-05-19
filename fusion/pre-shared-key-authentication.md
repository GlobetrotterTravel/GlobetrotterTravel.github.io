---
layout: default
title: Pre-shared key authentication
parent: Fusion
nav_order: 30
permalink: /fusion/pre-shared-key-authentication/
---

# Pre-shared key authentication
{: .no_toc}

Fusion supports pre-shared key (PSK) authentication as a straightforward method to access your data securely. In this method, a unique GUID (Globally Unique Identifier) is generated and shared with you. This GUID acts as your pre-shared key and must be included in the header of every request to Fusion.

## Table of contents
{: .no_toc}

1. TOC
{:toc}

## Overview

- **Service account creation**: Globetrotter creates a service account for your organization, associating it with one or more client numbers that you specify.
- **GUID generation**: A unique GUID is generated and securely transmitted to you.
- **Including the PSK in requests**: You include the PSK in the `psk` header of each request to authenticate with Fusion.
- **Data access**: If the PSK provided matches the one associated with your service account, the requested data is returned.

## Obtaining your pre-shared key

1. **Provide client numbers**: Inform your Globetrotter account manager of the client numbers you wish to access via Fusion.
2. **Service account setup**: Globetrotter will create a service account associated with the provided client numbers.
3. **Receive the PSK**: A unique GUID (your PSK) will be generated and securely transmitted to you. Ensure you store this PSK securely.

## Including the PSK in requests

You must include the PSK in the header of every HTTP request to Fusion. The header should be named `psk`, and its value should be the GUID provided to you.

### Example request

Below is an example of how to include the PSK header in a request using cURL:

```bash
curl -X GET "https://fusion.globetrotter.com.au/api/v1/invoices" \
     -H "psk: your-psk-guid"
```

### Important notes

> **⚠ Treat your PSK with the same level of security as a password. Do not share it or expose it in insecure environments.**
> **🛈 Ensure the header name is exactly `psk` in lowercase.**
> **🛈 All requests must include the `psk` header; otherwise, authentication will fail.**

## Rotating your PSK

If you believe your PSK has been compromised or wish to rotate it periodically for security reasons, contact your Globetrotter account manager to request a new PSK.

## Troubleshooting

- **401 Unauthorized**: If you receive a `401 Unauthorized` response, verify that:
  - The `psk` header is included in the request.
  - The PSK value is correct and matches the one provided to you.
- **403 Forbidden**: Ensure that your service account has access to the client numbers you are querying.
- **Contact support**: For any issues or questions, please reach out to the Globetrotter IT team for assistance.
