---
layout: default
title: SharePoint setup
parent: Globetrotter Fusion
nav_order: 40
permalink: /fusion/sharepoint-setup/
---

# SharePoint setup
{: .no_toc }

Fusion can send invoices to Microsoft SharePoint document folders, tagging each PDF with its custom data. 

This section is intended for client system administrators to properly prepare their SharePoint sites to receive invoices from Fusion.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Setting up your SharePoint instance

Ensure that you have administration rights to your SharePoint instance.

### Step 1: Create an Azure AD App Registration

1. Navigate to the [Azure Portal](https://portal.azure.com)
2. Sign in with your administrator account
3. Go to **Entra** > **App registrations**
4. Click **New registration**
5. Configure the registration:
   - **Name**: `Fusion Invoice Integration`
   - **Supported account types**: Select "Accounts in this organizational directory only"
   - **Redirect URI**: Leave blank
6. Click **Register**

**Important**: Copy the **Application (client) ID** from the overview page - you'll need this later.

### Step 2: Note Your Tenant ID

1. While still in Azure Active Directory, click **Overview** in the left navigation
2. Copy the **Tenant ID** - you'll need to provide this to the Globetrotter IT team

### Step 3: Generate a Client Secret

1. In your app registration, go to **Certificates & secrets**
2. Click **New client secret**
3. Add a description: `Fusion SharePoint Access`
4. Set expiration to **24 months** (or per your organization's policy)
5. Click **Add**
6. **Immediately copy the secret value** - it will not be shown again

{: .warning }

Store the client secret securely. You'll need to provide it to the Globetrotter IT team, and it cannot be retrieved from Azure after leaving this page.

### Step 4: Configure API Permissions

1. In your app registration, go to **API permissions**
2. Click **Add a permission**
3. Select **Microsoft Graph**
4. Choose **Application permissions**
5. Search for and select these permissions:
   - `Sites.ReadWrite.All` - Read and write items in all site collections
   - `Files.ReadWrite.All` - Read and write files in all site collections
6. Click **Add permissions**
7. Click **Grant admin consent for [Your Organization]**
8. Confirm by clicking **Yes**

{: .note }

The permissions will show a green checkmark when admin consent has been granted successfully.

### Step 5: Prepare Your SharePoint Site

1. Navigate to your SharePoint site where you want invoices to be stored
2. Note the exact URL (e.g., `https://yourcompany.sharepoint.com/sites/accounting`)
3. Create or identify the document library where invoices should be stored:
   - Common library names: "Documents", "Invoices", "Shared Documents"
   - If creating a new library, use **Site contents** > **New** > **Document library**
4. Optionally, create a folder structure within the library (e.g., "Vendor Invoices", "Fusion Invoices")

## Information to Provide to Globetrotter

Once you've completed the setup, provide the following information to the Globetrotter IT team:

| Setting | Description | Example |
|---------|-------------|---------|
| **Tenant ID** | Your Azure AD tenant identifier | `12345678-1234-1234-1234-123456789012` |
| **Client ID** | The application (client) ID from your app registration | `87654321-4321-4321-4321-210987654321` |
| **Client Secret** | The secret value you generated | `aBc123...` (32+ character string) |
| **SharePoint Site URL** | The full URL to your SharePoint site | `https://yourcompany.sharepoint.com/sites/accounting` |
| **Document Library** | The name of the document library for invoices | `Documents` or `Invoices` |
| **Target Folder** | (Optional) Specific folder within the library | `Vendor Invoices/Fusion` |

{: .warning }
Transmit the client secret securely (encrypted email, secure portal, or phone call). Do not send it in plain text email.

## Folder Structure and File Naming

Fusion will organize uploaded invoices using the format you specify. The following structure is typical:

```
📁 [Document Library]
  📁 [Target Folder] (if specified)
    📁 [Year] (e.g., 2025)
      📁 [Month] (e.g., 01-Jan)
        📄 20250115_143022_Invoice_12345.pdf
        📄 20250116_091500_Invoice_12346.pdf
```

Files are also named with the the pattern you specify.

### Available placeholder fields

The following placeholder fields can be used in your filename pattern and are also populated as SharePoint columns on each uploaded file.

#### Client info

| Placeholder | Description | SharePoint column |
|---|---|---|
| `clientNumber` | Client number from filename | Client Number |
| `clientName` | Organization name (entitlement) | Client Name |

#### Date components

Derived from the Dynamics invoice date.

| Placeholder | Description | Example | SharePoint column |
|---|---|---|---|
| `invoiceYear4` | 4-digit year | `2025` | Invoice Year |
| `invoiceYear2` | 2-digit year | `25` | Invoice Year Short |
| `invoiceMonth2` | 2-digit month | `01` | Invoice Month |
| `invoiceMonthName` | Full month name | `January` | Invoice Month Name |
| `invoiceDay2` | 2-digit day | `30` | Invoice Day |
| `invoiceDate` | Full date | `2025-01-30` | Invoice Date |

#### Dynamics invoice fields

| Placeholder | SharePoint column |
|---|---|
| `invoiceNumber` | Invoice Number |
| `totalAmountExcludingTax` | Total Amount Excl. |
| `totalTaxAmount` | Total Tax Amount |
| `totalAmountIncludingTax` | Total Amount Incl. |

#### Standard PDF fields

Extracted automatically from each invoice PDF.

| Placeholder | Example | SharePoint column |
|---|---|---|
| `recordLocator` | `DSJ4NZ` | Record Locator |
| `bookingType` | `Offline`, `Online` | Booking Type |

#### Custom data fields

Client-specific custom data field values can be placed directly in folders, filenames or SharePoint metadata columns.

The exact set of custom data fields available depends on your configuration. Speak to your CRM for the full list.

## Ongoing Maintenance

### Client Secret Renewal

Client secrets expire based on the duration you selected (typically 12-24 months):

1. **Before expiration**: Generate a new client secret following Step 3 above
2. **Provide the new secret** to the Globetrotter IT team before the old one expires
3. **Update your calendar** to remind you of the next renewal date

### Monitoring Access

To monitor Fusion's access to your SharePoint:

1. Go to **Azure Active Directory** > **Enterprise applications**
2. Find your `Fusion Invoice Integration` app
3. Click **Sign-in logs** to view access history
4. Review **Audit logs** for any permission changes

### Troubleshooting

**Common issues and solutions:**

| Issue | Solution |
|-------|----------|
| "Access denied" errors | Verify admin consent was granted for API permissions |
| App not found in SharePoint permissions | Wait 10-15 minutes for Azure AD changes to propagate |
| Files not appearing in expected folder | Confirm the exact document library name and folder path with Fusion team |
| Client secret expired | Generate new secret and provide to Fusion team immediately |

For additional support, contact the Globetrotter IT team with your **Tenant ID** and **Client ID** (never share the client secret in support requests).