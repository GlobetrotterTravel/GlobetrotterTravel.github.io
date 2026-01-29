---
layout: default
title: CreateSharePointFolder
parent: Iris API functions
has_children: false
has_toc: false
---

# CreateSharePointFolder

Creates a folder (and any missing parent folders) in SharePoint Online.

## Syntax

```jyro
CreateSharePointFolder(config, folderPath)
```

## Parameters

- **config** (object): SharePoint connection configuration
- **folderPath** (string): Server-relative folder path to create (e.g., `/Shared Documents/2024/January`)

## Configuration Object

The config object must contain:

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| siteUrl | string | Yes | Full SharePoint site URL (e.g., `https://tenant.sharepoint.com/sites/MySite`) |
| clientId | string | Yes | Azure AD application client ID |
| tenantId | string | Yes | Azure AD tenant ID |
| certificateBase64 | string | Yes | Base64-encoded .pfx certificate |
| certificatePassword | string | No | Password for the certificate |

## Returns

- **object**: Result object with properties:
  - **success** (boolean): Whether folder creation succeeded
  - **folderUrl** (string): URL of the created folder (on success)
  - **error** (string): Error message (on failure)

## Description

Creates folders in SharePoint document libraries, automatically creating any missing parent folders in the path (similar to `mkdir -p`). If the folder already exists, the function returns success.

This is commonly used before uploading files to ensure the destination folder exists.

### Required Azure AD Permissions

The Azure AD application needs:
- `Sites.ReadWrite.All` - For folder creation

## Examples

### Create a simple folder

```jyro
var config = {
    siteUrl: "https://contoso.sharepoint.com/sites/Documents",
    clientId: GetSecret("SP_CLIENT_ID"),
    tenantId: GetSecret("SP_TENANT_ID"),
    certificateBase64: GetSecret("SP_CERTIFICATE_BASE64")
}

var result = CreateSharePointFolder(config, "/Shared Documents/Reports")

if result.success then
    Log("Information", "Folder ready: " + result.folderUrl)
else
    Log("Error", "Failed to create folder: " + result.error)
end
```

### Create date-based folder structure

```jyro
var config = {
    siteUrl: "https://contoso.sharepoint.com/sites/Archive",
    clientId: GetSecret("SP_CLIENT_ID"),
    tenantId: GetSecret("SP_TENANT_ID"),
    certificateBase64: GetSecret("SP_CERTIFICATE_BASE64")
}

var year = FormatDate(Now(), "yyyy")
var month = FormatDate(Now(), "MM")
var day = FormatDate(Now(), "dd")

# Creates /Shared Documents/2024/01/15 (and all parent folders)
var folderPath = "/Shared Documents/" + year + "/" + month + "/" + day

var result = CreateSharePointFolder(config, folderPath)

if result.success then
    Log("Information", "Date folder created: " + result.folderUrl)
end
```

### Ensure folder exists before upload

```jyro
var config = {
    siteUrl: "https://contoso.sharepoint.com/sites/Finance",
    clientId: GetSecret("SP_CLIENT_ID"),
    tenantId: GetSecret("SP_TENANT_ID"),
    certificateBase64: GetSecret("SP_CERTIFICATE_BASE64")
}

# Determine folder based on document type
var documentType = Data.documentType  # e.g., "Invoice", "Receipt", "Contract"
var folderPath = "/Shared Documents/" + documentType + "s"

# Create folder if it doesn't exist
var folderResult = CreateSharePointFolder(config, folderPath)

if not folderResult.success then
    Log("Error", "Could not create folder: " + folderResult.error)
    Data._payload = { error: "Folder creation failed" }
    Data._statusCode = 500
    return
end

# Now upload the file
config.folderPath = "/sites/Finance" + folderPath

var uploadResult = UploadFileToSharePoint(config, "documents", Data.blobPath, [])

if uploadResult.success then
    Log("Information", "Document uploaded to " + documentType + " folder")
end
```

### Create folder per organization

```jyro
var config = {
    siteUrl: "https://contoso.sharepoint.com/sites/Clients",
    clientId: GetSecret("SP_CLIENT_ID"),
    tenantId: GetSecret("SP_TENANT_ID"),
    certificateBase64: GetSecret("SP_CERTIFICATE_BASE64")
}

var org = GetOrganizationById(Data.organizationId)
if org == null then
    Log("Error", "Organization not found")
    return
end

# Create folder using organization name (sanitized)
var orgFolderName = Replace(org.name, "/", "-")
orgFolderName = Replace(orgFolderName, "\\", "-")
orgFolderName = Replace(orgFolderName, ":", "-")

var folderPath = "/Shared Documents/Clients/" + orgFolderName

var result = CreateSharePointFolder(config, folderPath)

if result.success then
    Log("Information", "Client folder ready: " + result.folderUrl)

    # Store the SharePoint folder URL on the organization
    var urlFacet = GetFacetDefinitionByName("SharePoint Folder")
    if urlFacet != null then
        SetOrganizationFacetInstance(org.id, urlFacet.id, [result.folderUrl])
    end
end
```

### Batch folder creation

```jyro
var config = {
    siteUrl: "https://contoso.sharepoint.com/sites/Projects",
    clientId: GetSecret("SP_CLIENT_ID"),
    tenantId: GetSecret("SP_TENANT_ID"),
    certificateBase64: GetSecret("SP_CERTIFICATE_BASE64")
}

# Create standard project folder structure
var projectName = Data.projectName
var folders = [
    "/Shared Documents/Projects/" + projectName,
    "/Shared Documents/Projects/" + projectName + "/Documents",
    "/Shared Documents/Projects/" + projectName + "/Reports",
    "/Shared Documents/Projects/" + projectName + "/Deliverables"
]

var allCreated = true

foreach folderPath in folders do
    var result = CreateSharePointFolder(config, folderPath)

    if result.success then
        Log("Debug", "Created: " + folderPath)
    else
        Log("Warning", "Failed to create " + folderPath + ": " + result.error)
        allCreated = false
    end
end

if allCreated then
    Log("Information", "Project folder structure created successfully")
else
    Log("Warning", "Some folders could not be created")
end
```

### Handle existing folders

```jyro
var config = {
    siteUrl: "https://contoso.sharepoint.com/sites/HR",
    clientId: GetSecret("SP_CLIENT_ID"),
    tenantId: GetSecret("SP_TENANT_ID"),
    certificateBase64: GetSecret("SP_CERTIFICATE_BASE64")
}

# This is idempotent - succeeds even if folder exists
var result = CreateSharePointFolder(config, "/Shared Documents/Employee Records")

# success will be true whether folder was created or already existed
if result.success then
    Log("Information", "Folder is ready for use")
end
```

## Path Format Notes

- Paths should be relative to the site, not include the site URL
- Start paths with `/` for clarity
- Use forward slashes `/` as separators
- Common document library is `/Shared Documents/`
- Custom libraries use their display name (e.g., `/My Custom Library/`)

## Error Handling

Common errors:
- **Config missing required fields**: Check that all required config properties are provided
- **Failed to acquire token**: Certificate or credentials are invalid
- **Permission denied**: Application lacks `Sites.ReadWrite.All` permission
- **Site not found**: Invalid `siteUrl`

## Notes

- Creates all missing folders in the path (like `mkdir -p`)
- If folder already exists, returns success (idempotent operation)
- Folder names with special characters should work but may need escaping
- Uses certificate-based authentication for server-to-server communication

## Related Functions

- [UploadFileToSharePoint](UploadFileToSharePoint.md) - Upload files to SharePoint
- [GetSecret](../secrets/GetSecret.md) - Retrieve configuration secrets
