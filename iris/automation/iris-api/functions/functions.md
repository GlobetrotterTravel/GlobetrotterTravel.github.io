---
layout: default
title: Iris API functions
parent: Iris API
grand_parent: Automation
nav_order: 10
has_children: true
has_toc: false
permalink: /iris/automation/iris-api/functions/
---

# Iris API functions
{: .no_toc }

This section documents all host functions available in Iris Jyro scripts. These functions extend the Jyro scripting language with Iris-specific capabilities for managing organizations, persons, facets, secrets, notifications, and more.

## Function Categories

### Organizations
Functions for managing organization entities:
- [GetOrganizationById](organizations/GetOrganizationById) - Retrieve organization by ID
- [GetAllOrganizations](organizations/GetAllOrganizations) - Get all organizations
- [GetChildOrganizations](organizations/GetChildOrganizations) - Get child organizations
- [GetOrganizationsBySet](organizations/GetOrganizationsBySet) - Get organizations by set membership
- [CreateOrganization](organizations/CreateOrganization) - Create a new organization
- [SetOrganizationParent](organizations/SetOrganizationParent) - Set organization parent
- [DeleteOrganization](organizations/DeleteOrganization) - Delete an organization

### Persons
Functions for managing person entities:
- [GetPersonById](persons/GetPersonById) - Get person by ID
- [GetPersonByEmail](persons/GetPersonByEmail) - Get person by email address
- [GetAllPersons](persons/GetAllPersons) - Get all persons
- [GetAllPersonsByEmail](persons/GetAllPersonsByEmail) - Get all persons with matching email (find duplicates)
- [GetPersonsBySet](persons/GetPersonsBySet) - Get persons by set membership
- [CreatePerson](persons/CreatePerson) - Create a new person
- [UpdatePerson](persons/UpdatePerson) - Update an existing person
- [DeletePerson](persons/DeletePerson) - Delete a person
- [AddPersonToOrganization](persons/AddPersonToOrganization) - Add person to an organization
- [RemovePersonFromOrganization](persons/RemovePersonFromOrganization) - Remove person from an organization

### Facets
Functions for managing facet definitions and instances:
- [GetFacetDefinitionByName](facets/GetFacetDefinitionByName) - Get facet definition by name
- [GetAllFacetDefinitions](facets/GetAllFacetDefinitions) - Get all facet definitions
- [CreateFacetDefinition](facets/CreateFacetDefinition) - Create a new facet definition
- [GetOrganizationFacetInstance](facets/GetOrganizationFacetInstance) - Get single facet instance
- [GetOrganizationFacetInstances](facets/GetOrganizationFacetInstances) - Get all facet instances
- [SetOrganizationFacetInstance](facets/SetOrganizationFacetInstance) - Set organization facet
- [GetPersonFacetInstance](facets/GetPersonFacetInstance) - Get person facet instance
- [SetPersonFacetInstance](facets/SetPersonFacetInstance) - Set person facet instance

### Fields
Functions for managing field definitions and instances:
- [GetAllFieldDefinitions](fields/GetAllFieldDefinitions) - Get all field definitions
- [CreateFieldDefinition](fields/CreateFieldDefinition) - Create a new field definition
- [SetOrganizationFieldInstance](fields/SetOrganizationFieldInstance) - Set field instance items on organization
- [GetAllOrganizationFieldInstances](fields/GetAllOrganizationFieldInstances) - Get all field instances for organization
- [GetOrganizationFieldInstanceByName](fields/GetOrganizationFieldInstanceByName) - Get field instance by definition name

### Hybrid Lists
Functions for managing hybrid list definitions and instances:
- [GetAllHybridListDefinitions](hybridlists/GetAllHybridListDefinitions) - Get all hybrid list definitions
- [CreateHybridListDefinition](hybridlists/CreateHybridListDefinition) - Create a new hybrid list definition
- [CreateHybridListDefinitionCategory](hybridlists/CreateHybridListDefinitionCategory) - Create a category in a hybrid list
- [CreateHybridListDefinitionItem](hybridlists/CreateHybridListDefinitionItem) - Create an item in a hybrid list category
- [SetOrganizationHybridListInstance](hybridlists/SetOrganizationHybridListInstance) - Set hybrid list items on organization

### Entitlements
Functions for managing entitlement definitions and granting entitlements to organizations:
- [GetAllEntitlementDefinitions](entitlements/GetAllEntitlementDefinitions) - Get all entitlement definitions
- [GetEntitlementDefinitionByName](entitlements/GetEntitlementDefinitionByName) - Get entitlement definition by name
- [CreateEntitlementDefinition](entitlements/CreateEntitlementDefinition) - Create a new entitlement definition
- [DeleteEntitlementDefinition](entitlements/DeleteEntitlementDefinition) - Delete an entitlement definition
- [ActivateEntitlementDefinition](entitlements/ActivateEntitlementDefinition) - Activate an entitlement definition
- [DeactivateEntitlementDefinition](entitlements/DeactivateEntitlementDefinition) - Deactivate an entitlement definition
- [GetAllOrganizationEntitlementInstances](entitlements/GetAllOrganizationEntitlementInstances) - Get all entitlement instances for organization
- [GetOrganizationEntitlementInstanceByName](entitlements/GetOrganizationEntitlementInstanceByName) - Get entitlement instance by definition name
- [AddEntitlementInstance](entitlements/AddEntitlementInstance) - Grant entitlement to an organization
- [RemoveEntitlementInstance](entitlements/RemoveEntitlementInstance) - Revoke entitlement from an organization
- [RenewEntitlementInstance](entitlements/RenewEntitlementInstance) - Set new end date for entitlement instance

### Roles
Functions for managing person roles:
- [GetPersonRoles](roles/GetPersonRoles) - Get roles assigned to a person
- [GetCurrentUserRoles](roles/GetCurrentUserRoles) - Get roles of the current authenticated user
- [AssignRoleToPerson](roles/AssignRoleToPerson) - Assign a role to a person
- [RemoveRoleFromPerson](roles/RemoveRoleFromPerson) - Remove a role from a person

### Secrets
Functions for managing encrypted secrets:
- [GetSecret](secrets/GetSecret) - Retrieve decrypted secret
- [SetSecret](secrets/SetSecret) - Create or update secret
- [DeleteSecret](secrets/DeleteSecret) - Delete secret

### Notepads
Functions for persisting and retrieving script output data:
- [CreateNotepad](notepads/CreateNotepad) - Create a notepad to persist data
- [GetNotepads](notepads/GetNotepads) - Get notepads from current or specific execution
- [GetNotepadContent](notepads/GetNotepadContent) - Retrieve notepad content by ID
- [DeleteNotepad](notepads/DeleteNotepad) - Delete a notepad

### Notifications
Functions for real-time notifications via SignalR:
- [SendNotification](notifications/SendNotification) - Send to specific user
- [BroadcastNotification](notifications/BroadcastNotification) - Broadcast to all users

### Email
Functions for sending emails:
- [SendEmail](email/SendEmail) - Send email via Postmark

### Templating
Functions for template rendering:
- [RenderTemplate](templating/RenderTemplate) - Render Scriban templates

### Articles
Functions for news/article management:
- [CreateArticle](articles/CreateArticle) - Create home page articles

### Data Import
Functions for reading external data files:
- [ReadCsv](data/ReadCsv) - Read CSV files
- [ReadJson](data/ReadJson) - Read JSON files
- [ReadExcel](data/ReadExcel) - Read Excel (.xlsx) files
- [ReadXml](data/ReadXml) - Read XML files

### File Generation
Functions for generating files:
- [GenerateExcel](files/GenerateExcel) - Generate Excel with temporary download URL (1-hour expiry)
- [GenerateExcelBytes](blobstorage/GenerateExcelBytes) - Generate Excel and return raw bytes for blob storage

### Blob Storage
Functions for persistent file storage using Azure Blob Storage. Requires tenant configuration (see [Blob Storage Setup](../blob-storage-setup)):
- [IsBlobStorageEnabled](blobstorage/IsBlobStorageEnabled) - Check if blob storage is enabled
- [WriteToBlobStorage](blobstorage/WriteToBlobStorage) - Upload file to blob storage
- [ReadFromBlobStorage](blobstorage/ReadFromBlobStorage) - Download file from blob storage
- [ListBlobStorage](blobstorage/ListBlobStorage) - List files in blob storage
- [DeleteFromBlobStorage](blobstorage/DeleteFromBlobStorage) - Delete file from blob storage
- [BlobStorageExists](blobstorage/BlobStorageExists) - Check if file exists
- [GetBlobStorageSasUrl](blobstorage/GetBlobStorageSasUrl) - Generate time-limited download URL

### External Blob Storage
Functions for working with external Azure Blob Storage accounts (not the tenant's configured storage):
- [DownloadBlobFiles](externalblob/DownloadBlobFiles) - Download files from external blob storage
- [MoveBlob](externalblob/MoveBlob) - Move blobs within a container

### File Handles
Functions for working with downloaded file handles:
- [ReadFileContent](files/ReadFileContent) - Read text content of a downloaded file
- [GetFileHandle](files/GetFileHandle) - Get file handle metadata

### PDF Processing
Functions for extracting data from PDF documents:
- [ReadCustomDataFromPdf](pdf/ReadCustomDataFromPdf) - Extract key-value pairs from PDFs using label mappings

### SQL Database
Functions for querying external SQL Server databases:
- [SqlConnect](sql/SqlConnect) - Establish a database connection
- [SqlQuery](sql/SqlQuery) - Execute SELECT queries
- [SqlExecute](sql/SqlExecute) - Execute INSERT/UPDATE/DELETE commands
- [SqlClose](sql/SqlClose) - Close a database connection

### SharePoint Integration
Functions for uploading files and creating folders in SharePoint Online:
- [UploadFileToSharePoint](sharepoint/UploadFileToSharePoint) - Upload files to SharePoint with metadata
- [CreateSharePointFolder](sharepoint/CreateSharePointFolder) - Create folders in SharePoint

### Context
Functions for accessing current execution context:
- [GetCurrentUser](context/GetCurrentUser) - Get authenticated user info
- [GetCurrentTenant](context/GetCurrentTenant) - Get current tenant info

### Audit
Functions for accessing audit trail data:
- [GetRecentAuditEntries](audit/GetRecentAuditEntries) - Get recent audit entries for the tenant

### Utility
Core utility functions:
- [Log](utility/Log) - Write to application logs
- [CancelAction](utility/CancelAction) - Cancel current operation (Before hooks only)

## Usage Context

These functions are available in three execution contexts:

1. **Event Hooks** - Scripts triggered by domain events (BeforeOrganizationCreated, AfterPersonUpdated, etc.)
2. **Dynamic Endpoints** - Custom API endpoints powered by Jyro scripts
3. **Ad-hoc Scripts** - Administrative scripts executed on-demand

For detailed information about the `Data` object structure passed to scripts in each context, see the [Data Context Reference](../data-context).

## Jyro Syntax Reminder

Remember that Jyro uses its own syntax, not JavaScript:

```jyro
# Conditionals
if condition then
    # code
end

# Loops
foreach item in items do
    # code
end

# Boolean operators
if isActive and age > 18 then
    # code
end
```

See the [Jyro documentation](https://meschsystems.github.io/jyro/) for complete language reference.

## Best Practices

1. **Always validate inputs** - Check for null/empty values before using
2. **Use appropriate log levels** - Debug, Information, Warning, Error
3. **Handle errors gracefully** - Check return values and log failures
4. **Use CancelAction wisely** - Only in Before hooks, with clear reason
5. **Keep scripts simple** - Complex logic belongs in the domain layer
