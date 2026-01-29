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
- [GetOrganizationById](organizations/GetOrganizationById.md) - Retrieve organization by ID
- [GetAllOrganizations](organizations/GetAllOrganizations.md) - Get all organizations
- [GetChildOrganizations](organizations/GetChildOrganizations.md) - Get child organizations
- [GetOrganizationsBySet](organizations/GetOrganizationsBySet.md) - Get organizations by set membership
- [CreateOrganization](organizations/CreateOrganization.md) - Create a new organization
- [SetOrganizationParent](organizations/SetOrganizationParent.md) - Set organization parent
- [DeleteOrganization](organizations/DeleteOrganization.md) - Delete an organization

### Persons
Functions for managing person entities:
- [GetPersonById](persons/GetPersonById.md) - Get person by ID
- [GetPersonByEmail](persons/GetPersonByEmail.md) - Get person by email address
- [GetAllPersons](persons/GetAllPersons.md) - Get all persons
- [GetAllPersonsByEmail](persons/GetAllPersonsByEmail.md) - Get all persons with matching email (find duplicates)
- [GetPersonsBySet](persons/GetPersonsBySet.md) - Get persons by set membership
- [CreatePerson](persons/CreatePerson.md) - Create a new person
- [UpdatePerson](persons/UpdatePerson.md) - Update an existing person
- [DeletePerson](persons/DeletePerson.md) - Delete a person
- [AddPersonToOrganization](persons/AddPersonToOrganization.md) - Add person to an organization
- [RemovePersonFromOrganization](persons/RemovePersonFromOrganization.md) - Remove person from an organization

### Facets
Functions for managing facet definitions and instances:
- [GetFacetDefinitionByName](facets/GetFacetDefinitionByName.md) - Get facet definition by name
- [GetAllFacetDefinitions](facets/GetAllFacetDefinitions.md) - Get all facet definitions
- [CreateFacetDefinition](facets/CreateFacetDefinition.md) - Create a new facet definition
- [GetOrganizationFacetInstance](facets/GetOrganizationFacetInstance.md) - Get single facet instance
- [GetOrganizationFacetInstances](facets/GetOrganizationFacetInstances.md) - Get all facet instances
- [SetOrganizationFacetInstance](facets/SetOrganizationFacetInstance.md) - Set organization facet
- [GetPersonFacetInstance](facets/GetPersonFacetInstance.md) - Get person facet instance
- [SetPersonFacetInstance](facets/SetPersonFacetInstance.md) - Set person facet instance

### Fields
Functions for managing field definitions and instances:
- [GetAllFieldDefinitions](fields/GetAllFieldDefinitions.md) - Get all field definitions
- [CreateFieldDefinition](fields/CreateFieldDefinition.md) - Create a new field definition
- [SetOrganizationFieldInstance](fields/SetOrganizationFieldInstance.md) - Set field instance items on organization
- [GetAllOrganizationFieldInstances](fields/GetAllOrganizationFieldInstances.md) - Get all field instances for organization
- [GetOrganizationFieldInstanceByName](fields/GetOrganizationFieldInstanceByName.md) - Get field instance by definition name

### Hybrid Lists
Functions for managing hybrid list definitions and instances:
- [GetAllHybridListDefinitions](hybridlists/GetAllHybridListDefinitions.md) - Get all hybrid list definitions
- [CreateHybridListDefinition](hybridlists/CreateHybridListDefinition.md) - Create a new hybrid list definition
- [CreateHybridListDefinitionCategory](hybridlists/CreateHybridListDefinitionCategory.md) - Create a category in a hybrid list
- [CreateHybridListDefinitionItem](hybridlists/CreateHybridListDefinitionItem.md) - Create an item in a hybrid list category
- [SetOrganizationHybridListInstance](hybridlists/SetOrganizationHybridListInstance.md) - Set hybrid list items on organization

### Entitlements
Functions for managing entitlement definitions and granting entitlements to organizations:
- [GetAllEntitlementDefinitions](entitlements/GetAllEntitlementDefinitions.md) - Get all entitlement definitions
- [GetEntitlementDefinitionByName](entitlements/GetEntitlementDefinitionByName.md) - Get entitlement definition by name
- [CreateEntitlementDefinition](entitlements/CreateEntitlementDefinition.md) - Create a new entitlement definition
- [DeleteEntitlementDefinition](entitlements/DeleteEntitlementDefinition.md) - Delete an entitlement definition
- [ActivateEntitlementDefinition](entitlements/ActivateEntitlementDefinition.md) - Activate an entitlement definition
- [DeactivateEntitlementDefinition](entitlements/DeactivateEntitlementDefinition.md) - Deactivate an entitlement definition
- [GetAllOrganizationEntitlementInstances](entitlements/GetAllOrganizationEntitlementInstances.md) - Get all entitlement instances for organization
- [GetOrganizationEntitlementInstanceByName](entitlements/GetOrganizationEntitlementInstanceByName.md) - Get entitlement instance by definition name
- [AddEntitlementInstance](entitlements/AddEntitlementInstance.md) - Grant entitlement to an organization
- [RemoveEntitlementInstance](entitlements/RemoveEntitlementInstance.md) - Revoke entitlement from an organization
- [RenewEntitlementInstance](entitlements/RenewEntitlementInstance.md) - Set new end date for entitlement instance

### Roles
Functions for managing person roles:
- [GetPersonRoles](roles/GetPersonRoles.md) - Get roles assigned to a person
- [GetCurrentUserRoles](roles/GetCurrentUserRoles.md) - Get roles of the current authenticated user
- [AssignRoleToPerson](roles/AssignRoleToPerson.md) - Assign a role to a person
- [RemoveRoleFromPerson](roles/RemoveRoleFromPerson.md) - Remove a role from a person

### Secrets
Functions for managing encrypted secrets:
- [GetSecret](secrets/GetSecret.md) - Retrieve decrypted secret
- [SetSecret](secrets/SetSecret.md) - Create or update secret
- [DeleteSecret](secrets/DeleteSecret.md) - Delete secret

### Notepads
Functions for persisting and retrieving script output data:
- [CreateNotepad](notepads/CreateNotepad.md) - Create a notepad to persist data
- [GetNotepads](notepads/GetNotepads.md) - Get notepads from current or specific execution
- [GetNotepadContent](notepads/GetNotepadContent.md) - Retrieve notepad content by ID
- [DeleteNotepad](notepads/DeleteNotepad.md) - Delete a notepad

### Notifications
Functions for real-time notifications via SignalR:
- [SendNotification](notifications/SendNotification.md) - Send to specific user
- [BroadcastNotification](notifications/BroadcastNotification.md) - Broadcast to all users

### Email
Functions for sending emails:
- [SendEmail](email/SendEmail.md) - Send email via Postmark

### Templating
Functions for template rendering:
- [RenderTemplate](templating/RenderTemplate.md) - Render Scriban templates

### Articles
Functions for news/article management:
- [CreateArticle](articles/CreateArticle.md) - Create home page articles

### Data Import
Functions for reading external data files:
- [ReadCsv](data/ReadCsv.md) - Read CSV files
- [ReadJson](data/ReadJson.md) - Read JSON files
- [ReadExcel](data/ReadExcel.md) - Read Excel (.xlsx) files
- [ReadXml](data/ReadXml.md) - Read XML files

### File Generation
Functions for generating files:
- [GenerateExcel](files/GenerateExcel.md) - Generate Excel with temporary download URL (1-hour expiry)
- [GenerateExcelBytes](blobstorage/GenerateExcelBytes.md) - Generate Excel and return raw bytes for blob storage

### Blob Storage
Functions for persistent file storage using Azure Blob Storage. Requires tenant configuration (see [Blob Storage Setup](../blob-storage-setup.md)):
- [IsBlobStorageEnabled](blobstorage/IsBlobStorageEnabled.md) - Check if blob storage is enabled
- [WriteToBlobStorage](blobstorage/WriteToBlobStorage.md) - Upload file to blob storage
- [ReadFromBlobStorage](blobstorage/ReadFromBlobStorage.md) - Download file from blob storage
- [ListBlobStorage](blobstorage/ListBlobStorage.md) - List files in blob storage
- [DeleteFromBlobStorage](blobstorage/DeleteFromBlobStorage.md) - Delete file from blob storage
- [BlobStorageExists](blobstorage/BlobStorageExists.md) - Check if file exists
- [GetBlobStorageSasUrl](blobstorage/GetBlobStorageSasUrl.md) - Generate time-limited download URL

### External Blob Storage
Functions for working with external Azure Blob Storage accounts (not the tenant's configured storage):
- [DownloadBlobFiles](externalblob/DownloadBlobFiles.md) - Download files from external blob storage
- [MoveBlob](externalblob/MoveBlob.md) - Move blobs within a container

### File Handles
Functions for working with downloaded file handles:
- [ReadFileContent](files/ReadFileContent.md) - Read text content of a downloaded file
- [GetFileHandle](files/GetFileHandle.md) - Get file handle metadata

### PDF Processing
Functions for extracting data from PDF documents:
- [ReadCustomDataFromPdf](pdf/ReadCustomDataFromPdf.md) - Extract key-value pairs from PDFs using label mappings

### SQL Database
Functions for querying external SQL Server databases:
- [SqlConnect](sql/SqlConnect.md) - Establish a database connection
- [SqlQuery](sql/SqlQuery.md) - Execute SELECT queries
- [SqlExecute](sql/SqlExecute.md) - Execute INSERT/UPDATE/DELETE commands
- [SqlClose](sql/SqlClose.md) - Close a database connection

### SharePoint Integration
Functions for uploading files and creating folders in SharePoint Online:
- [UploadFileToSharePoint](sharepoint/UploadFileToSharePoint.md) - Upload files to SharePoint with metadata
- [CreateSharePointFolder](sharepoint/CreateSharePointFolder.md) - Create folders in SharePoint

### Context
Functions for accessing current execution context:
- [GetCurrentUser](context/GetCurrentUser.md) - Get authenticated user info
- [GetCurrentTenant](context/GetCurrentTenant.md) - Get current tenant info

### Audit
Functions for accessing audit trail data:
- [GetRecentAuditEntries](audit/GetRecentAuditEntries.md) - Get recent audit entries for the tenant

### Utility
Core utility functions:
- [Log](utility/Log.md) - Write to application logs
- [CancelAction](utility/CancelAction.md) - Cancel current operation (Before hooks only)

## Usage Context

These functions are available in three execution contexts:

1. **Event Hooks** - Scripts triggered by domain events (BeforeOrganizationCreated, AfterPersonUpdated, etc.)
2. **Dynamic Endpoints** - Custom API endpoints powered by Jyro scripts
3. **Ad-hoc Scripts** - Administrative scripts executed on-demand

For detailed information about the `Data` object structure passed to scripts in each context, see the [Data Context Reference](../data-context.md).

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
