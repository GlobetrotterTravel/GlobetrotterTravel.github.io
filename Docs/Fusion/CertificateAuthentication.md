# Certificate authentication

Fusion's preferred authentication method is certificate-based authentication. Certificate-based authentication verifies a client by validating its digital certificate, which contains a unique fingerprint. This fingerprint is derived from the certificate's data using a cryptographic hash function, ensuring its uniqueness and integrity. The process protects client data through four important aspects of certificate security:

- **Uniqueness of fingerprint**: An SSL certificate's fingerprint (or hash) is designed to be unique to that particular certificate. The fingerprint is a hash value derived from the certificate's data, ensuring that any small change in the certificate results in a significantly different fingerprint.

- **Collision resistance**: It is virtually impossible for two different certificates to have the same fingerprint due to the properties of cryptographic hash functions. This property is known as collision resistance, and modern hash functions are designed to minimize the likelihood of collisions.

- **Reverse engineering**: It is computationally infeasible to reverse engineer the original certificate data from its fingerprint. Hash functions are one-way functions, meaning they are designed to be easy to compute in one direction (from data to hash) but extremely difficult to reverse.

- **Construct a certificate with the same fingerprint**: It is virtually impossible to construct a different certificate that results in the same fingerprint as a given certificate. This property is known as pre-image resistance and is a crucial feature of cryptographic hash functions.

These security measures make certificate-based authentication a robust and secure way to protect client data from unauthorised access.

## Certificate set up

For Fusion, Globetrotter encourages clients to generate and keep their own secure certificates. This way, certificates are part of the client's existing certificate chain and subject to the same security measures as other certificates.

The section provides instructions on how to generate a self-signed certificate or use an existing signing certificate to authenticate your organisation for Globetrotter Fusion. This guide assumes the use of PowerShell and OpenSSL. 

## Generating a Certificate and Extracting its SHA-256 Fingerprint

### Prerequisites

Ensure that you have an up to date copy of OpenSSL. OpenSSL is bundled with Git for Windows, and the following alias will point to the correct OpenSSL executable.

### Step 1: Set OpenSSL Alias

For convenience, set an alias in PowerShell:

```powershell
Set-Alias -Name openssl -Value 'C:\Program Files\Git\usr\bin\openssl.exe'
```

This command sets the `openssl` alias to the version provided by Git for Windows. If you are using an alternative OpenSSL executable you can alias it.

### Step 2: Generate a Private Key

A private key is required to create a certificate. Generate a 2048-bit RSA private key:

```powershell
openssl genrsa -out private.key 2048
```

This command creates a file named `private.key` containing your private key.

### Step 3: Create a Certificate Signing Request (CSR)

A Certificate Signing Request (CSR) is necessary to provide information about your certificate. Generate a CSR:

```powershell
openssl req -new -key private.key -out request.csr
```

During this step, you will be prompted to enter information about your organization. This information is used to populate the certificate fields.

### Step 4: Generate the Self-Signed Certificate

Use the CSR to generate a self-signed certificate valid for 365 days:

```powershell
openssl x509 -req -days 365 -in request.csr -signkey private.key -out certificate.crt
```

This command creates a file named `certificate.crt` containing your self-signed certificate.

## Using an Existing Signing Certificate

If you already have a signing certificate and wish to use it, follow these steps:

### Step 5: Generate a Private Key (if not already done)

If you do not have a private key, generate one:

```powershell
openssl genrsa -out private.key 2048
```

### Step 6: Create a Certificate Signing Request (CSR)

Generate a CSR using your private key:

```powershell
openssl req -new -key private.key -out request.csr
```

### Step 7: Sign the CSR with Your Existing Certificate

Assuming you have a certificate (`signing-cert.crt`) and its private key (`signing-key.key`), sign the CSR:

```powershell
openssl x509 -req -in request.csr -CA signing-cert.crt -CAkey signing-key.key -CAcreateserial -out certificate.crt -days 365
```

This command creates a file named `certificate.crt` containing your signed certificate.

### Step 8: Extract the SHA-256 Fingerprint

Extract the SHA-256 fingerprint of your certificate:

```powershell
openssl x509 -in certificate.crt -noout -fingerprint -sha256
```

The output will be similar to:

```
SHA256 Fingerprint=XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX
```

Copy the entire fingerprint (including the "SHA256 Fingerprint=" part) and provide it to us so we can create your Fusion service account.

## Create a PFX
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

   During the export process, OpenSSL will prompt you to set an export password. This password will be required when importing the PFX file into other systems. It's important to keep this password secure and accessible.

### Example

```powershell
openssl pkcs12 -export -out certificate.pfx -inkey private.key -in certificate.crt -certfile chain.crt
```

- The `certificate.pfx` is the name of the resulting PFX file.
- The `private.key` is your private key file.
- The `certificate.crt` is your public certificate.
- The `chain.crt` is the chain of trust (intermediate certificates) if applicable.

After running the command, you will have a `certificate.pfx` file that contains your private key, certificate, and any intermediate certificates. This file can be imported into various systems and applications that support the PKCS#12 format for sending to Fusion with each request.

## Advise Globetrotter of the fingerprint

Advise your CRM of the fingerprint for your certificate (do not send us the certificate itself). Ensure that the correct fingerprint is sent to us as we will create a service account for this fingerprint and bind it to one or more of your client numbers that you specify. Once this is done, you can use your certificate to access your data.

## Include with requests

Your certificate should be sent to Fusion with every request. If there are any issues, please contact the Globetrotter IT team for assistance.

<!-- Styled Footer Navigation for CertificateAuthentication.md -->
<div style="display: flex; justify-content: space-between; width:95%; padding: 10px; background-color: #f8f8f8; border: 1px solid #ddd; border-radius: 5px; margin: 20px auto 0 auto;">
  <div style="text-align:left;"><a href="Setup.html" style="text-decoration:none; color:#007acc;">&larr; Getting ready to use Fusion</a></div>
  <div style="text-align:center;"><a href="index.html" style="text-decoration:none; color:#007acc;">Home</a></div>
  <div style="text-align:right;"><a href="PSKAuthentication.html" style="text-decoration:none; color:#007acc;">Pre-Shared Key Authentication &rarr;</a></div>
</div>
