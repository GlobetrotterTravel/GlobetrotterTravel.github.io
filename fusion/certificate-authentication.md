---
layout: default
title: Certificate authentication
parent: Fusion
nav_order: 20
permalink: /fusion/certificate-authentication/
---

# Certificate authentication

Fusion's preferred authentication method is certificate-based authentication. Certificate-based authentication verifies a client by validating its digital certificate, which contains a unique fingerprint. This fingerprint is derived from the certificate's data using a cryptographic hash function, ensuring its uniqueness and integrity. The process protects client data through four important aspects of certificate security:

- **Uniqueness of fingerprint**: An SSL certificate's fingerprint (or hash) is designed to be unique to that particular certificate. The fingerprint is a hash value derived from the certificate's data, ensuring that any small change in the certificate results in a significantly different fingerprint.

- **Collision resistance**: It is virtually impossible for two different certificates to have the same fingerprint due to the properties of cryptographic hash functions. This property is known as collision resistance, and modern hash functions are designed to minimize the likelihood of collisions.

- **Reverse engineering**: It is computationally infeasible to reverse engineer the original certificate data from its fingerprint. Hash functions are one-way functions, meaning they are designed to be easy to compute in one direction (from data to hash) but extremely difficult to reverse.

- **Constructing a certificate with the same fingerprint**: It is virtually impossible to construct a different certificate that results in the same fingerprint as a given certificate. This property is known as pre-image resistance and is a crucial feature of cryptographic hash functions.

These security measures make certificate-based authentication a robust and secure way to protect client data from unauthorized access.

## Certificate setup

For Fusion, Globetrotter encourages clients to generate and keep their own secure certificates. This way, certificates are part of the client's existing certificate chain and subject to the same security measures as the client's other certificates.

This section provides instructions on how to generate a self-signed certificate or use an existing signing certificate to authenticate your organization for Globetrotter Fusion. This guide assumes the use of PowerShell and OpenSSL.

## Generating a certificate and extracting its SHA-256 fingerprint

### Prerequisites

Ensure that you have an up-to-date copy of OpenSSL. OpenSSL is bundled with Git for Windows, and the following alias will point to the correct OpenSSL executable.

### Step 1: Set OpenSSL alias

For convenience, set an alias in PowerShell:

```powershell
Set-Alias -Name openssl -Value 'C:\Program Files\Git\usr\bin\openssl.exe'
```

This command sets the `openssl` alias to the version provided by Git for Windows. If you are using an alternative OpenSSL executable, adjust the alias accordingly.

### Step 2: Generate a private key

A private key is required to create a certificate. Generate a 2048-bit RSA private key:

```powershell
openssl genrsa -out private.key 2048
```

This command creates a file named `private.key` containing your private key.

### Step 3: Create a certificate signing request (CSR)

A Certificate Signing Request (CSR) is necessary to provide information about your certificate. Generate a CSR:

```powershell
openssl req -new -key private.key -out request.csr
```

During this step, you will be prompted to enter information about your organization. This information is used to populate the certificate fields.

### Step 4: Generate the self-signed certificate

Use the CSR to generate a self-signed certificate valid for 365 days:

```powershell
openssl x509 -req -days 365 -in request.csr -signkey private.key -out certificate.crt
```

This command creates a file named `certificate.crt` containing your self-signed certificate.

## Using an existing signing certificate

If you already have a signing certificate and wish to use it, follow these steps:

### Step 1: Generate a private key (if not already done)

If you do not have a private key, generate one:

```powershell
openssl genrsa -out private.key 2048
```

### Step 2: Create a certificate signing request (CSR)

Generate a CSR using your private key:

```powershell
openssl req -new -key private.key -out request.csr
```

### Step 3: Sign the CSR with your existing certificate

Assuming you have a certificate (`signing-cert.crt`) and its private key (`signing-key.key`), sign the CSR:

```powershell
openssl x509 -req -in request.csr -CA signing-cert.crt -CAkey signing-key.key -CAcreateserial -out certificate.crt -days 365
```

This command creates a file named `certificate.crt` containing your signed certificate.

### Step 4: Extract the SHA-256 fingerprint

Extract the SHA-256 fingerprint of your certificate:

```powershell
openssl x509 -in certificate.crt -noout -fingerprint -sha256
```

The output will be similar to:

```
SHA256 Fingerprint=XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX
```

Copy the entire fingerprint (including the "SHA256 Fingerprint=" part) and provide it to us so we can create your Fusion service account.

## Creating a PFX file

To create a PFX (PKCS#12) file from an existing set of certificate files using OpenSSL, ensure you have three components:

1. **The private key file** (e.g., `private.key`)
2. **The public certificate file** (e.g., `certificate.crt`)
3. **The certificate chain or intermediate certificate file** (if applicable, e.g., `chain.crt`)

### Combine the certificate and private key into a PFX file

If you have both the certificate and the private key, you can combine them into a PFX file using the following command:

```powershell
openssl pkcs12 -export -out certificate.pfx -inkey private.key -in certificate.crt
```

### Include the certificate chain (if applicable)

If your certificate requires a chain of trust (intermediate certificates), include the chain file using the `-certfile` option:

```powershell
openssl pkcs12 -export -out certificate.pfx -inkey private.key -in certificate.crt -certfile chain.crt
```

### Specify a password

During the export process, OpenSSL will prompt you to set an export password. This password will be required when importing the PFX file into other systems. It's important to keep this password secure and accessible. If you don't want to specify a password, just press enter.

### Example

```powershell
openssl pkcs12 -export -out certificate.pfx -inkey private.key -in certificate.crt -certfile chain.crt
```

- The `certificate.pfx` is the name of the resulting PFX file.
- The `private.key` is your private key file.
- The `certificate.crt` is your public certificate.
- The `chain.crt` is the chain of trust (intermediate certificates), if applicable.

After running the command, you will have a `certificate.pfx` file that contains your private key, certificate, and any intermediate certificates. This file can be imported into various systems and applications that support the PKCS#12 format for sending to Fusion with each request.

## Notify Globetrotter of the fingerprint

Please provide the SHA-256 fingerprint of your certificate to your Globetrotter account manager (do not send the certificate itself). Ensure that the correct fingerprint is sent to us, as we will create a service account for this fingerprint and bind it to one or more of your client numbers that you specify. Once this is done, you can use your certificate to access your data.

## Including the certificate with requests

Your certificate should be sent to Fusion with every request. This is typically done by configuring your HTTP client to use the certificate for client authentication (mutual TLS). Consult your development framework or tool's documentation on how to include client certificates with HTTP requests.

**Example in cURL**:

```bash
curl -X GET "https://fusion.globetrotter.com.au/api/v1/invoices" \
     --cert /path/to/certificate.pfx --cert-type P12
```

Replace `/path/to/certificate.pfx` with the path to your PFX file. You may need to provide the certificate password if prompted.

If you have any issues, please contact the Globetrotter IT team for assistance.
