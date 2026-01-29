---
layout: default
title: ReadCustomDataFromPdf
parent: Iris API functions
has_children: false
has_toc: false
---

# ReadCustomDataFromPdf

Extracts key-value pairs from a PDF stored in Azure Blob Storage using label-to-field mappings.

## Syntax

```jyro
ReadCustomDataFromPdf(connectionString, containerName, blobPath, mappings)
ReadCustomDataFromPdf(connectionString, containerName, blobPath, mappings, stopWord)
```

## Parameters

- **connectionString** (string): Azure Blob Storage connection string (typically from a secret)
- **containerName** (string): The Azure Blob Storage container name
- **blobPath** (string): Path to the PDF blob
- **mappings** (array): Array of label-to-field mapping objects
- **stopWord** (string, optional): Text boundary where extraction stops (default: `"Product"`)

## Mapping Object Structure

Each mapping object should contain:
- **label** (string): The text label to search for in the PDF
- **field** (string): The field name to assign to the extracted value

## Returns

- **object**: Result object with properties:
  - **success** (boolean): Whether extraction succeeded
  - **kvps** (array): Array of extracted key-value pairs `[{key: "fieldName", value: "extracted value"}, ...]`
  - **rawText** (string): Raw extracted text for debugging
  - **error** (string): Error message (on failure)

## Description

Extracts structured data from PDFs by searching for text labels and capturing the values that follow them. This is useful for processing forms, invoices, or any PDF with consistent label-value formatting.

The function:
1. Streams the PDF from Azure Blob Storage
2. Extracts all text content
3. Locates each specified label in the text
4. Captures the text between each label and the next label (or stop word)
5. Returns the extracted values mapped to your specified field names

### How Extraction Works

Labels are found in order of their position in the document. The value for each label is the text between that label and the next label (or the stop word boundary).

For example, with labels "Name:" and "Email:" in a document:
```
Name: John Smith Email: john@example.com Product Details...
```

Would extract:
- `Name` → "John Smith"
- `Email` → "john@example.com"

## Examples

### Basic extraction

```jyro
var connectionString = GetSecret("INVOICE_STORAGE_CONNECTION")

var mappings = [
    { label: "Invoice Number:", field: "invoiceNumber" },
    { label: "Invoice Date:", field: "invoiceDate" },
    { label: "Total Amount:", field: "totalAmount" },
    { label: "Customer:", field: "customerName" }
]

var result = ReadCustomDataFromPdf(connectionString, "invoices", "pending/invoice-001.pdf", mappings)

if result.success then
    foreach kvp in result.kvps do
        Log("Information", kvp.key + " = " + kvp.value)
    end
else
    Log("Error", "Extraction failed: " + result.error)
end
```

### Travel request processing

```jyro
var connectionString = GetSecret("TRAVEL_DOCS_CONNECTION")

var mappings = [
    { label: "Reason for Travel", field: "travelReason" },
    { label: "Destination", field: "destination" },
    { label: "Departure Date", field: "departureDate" },
    { label: "Return Date", field: "returnDate" },
    { label: "Estimated Cost", field: "estimatedCost" },
    { label: "Job Code", field: "jobCode" }
]

var result = ReadCustomDataFromPdf(connectionString, "requests", "new/travel-request.pdf", mappings, "Approval Section")

if result.success then
    # Build an object from extracted values
    var request = {}
    foreach kvp in result.kvps do
        request[kvp.key] = Trim(kvp.value)
    end

    Log("Information", "Travel to " + request.destination + " for: " + request.travelReason)

    # Store for further processing
    Notepad({
        type: "TravelRequest",
        extractedAt: Now(),
        data: request
    })
end
```

### Process multiple PDFs

```jyro
var connectionString = GetSecret("FORMS_STORAGE_CONNECTION")

var mappings = [
    { label: "Employee Name", field: "employeeName" },
    { label: "Department", field: "department" },
    { label: "Request Type", field: "requestType" }
]

# List PDFs in the pending folder
var files = DownloadBlobFiles(connectionString, "forms", "pending/", 50)

var processed = 0
var failed = 0

foreach file in files do
    if not EndsWith(file.fileName, ".pdf") then
        continue
    end

    var result = ReadCustomDataFromPdf(connectionString, "forms", file.blobName, mappings)

    if result.success then
        processed = processed + 1

        # Log extracted data
        foreach kvp in result.kvps do
            Log("Debug", file.fileName + " - " + kvp.key + ": " + kvp.value)
        end

        # Move to processed folder
        MoveBlob(connectionString, "forms", file.blobName, "processed/" + file.fileName)
    else
        failed = failed + 1
        Log("Warning", "Failed to process " + file.fileName + ": " + result.error)
        MoveBlob(connectionString, "forms", file.blobName, "failed/" + file.fileName)
    end
end

Log("Information", "Processed " + processed + " PDFs, " + failed + " failed")
```

### Use extracted data to update organization

```jyro
var connectionString = GetSecret("VENDOR_DOCS_CONNECTION")

var mappings = [
    { label: "Vendor Name", field: "vendorName" },
    { label: "Tax ID", field: "taxId" },
    { label: "Address", field: "address" },
    { label: "Contact Email", field: "contactEmail" }
]

var result = ReadCustomDataFromPdf(connectionString, "vendor-forms", Data.pdfPath, mappings, "Signature")

if not result.success then
    Log("Error", "Could not extract vendor data: " + result.error)
    Data._payload = { error: "PDF extraction failed" }
    Data._statusCode = 400
    return
end

# Convert kvps array to object for easier access
var vendorData = {}
foreach kvp in result.kvps do
    vendorData[kvp.key] = Trim(kvp.value)
end

# Find or create the organization
var org = GetOrganizationById(Data.organizationId)
if org != null then
    # Update facets with extracted data
    var taxIdFacet = GetFacetDefinitionByName("Tax ID")
    if taxIdFacet != null and vendorData.taxId != null then
        SetOrganizationFacetInstance(org.id, taxIdFacet.id, [vendorData.taxId])
    end

    Log("Information", "Updated organization with PDF data")
end
```

### Debugging extraction

```jyro
var connectionString = GetSecret("DEBUG_STORAGE_CONNECTION")

var mappings = [
    { label: "Field 1", field: "field1" },
    { label: "Field 2", field: "field2" }
]

var result = ReadCustomDataFromPdf(connectionString, "test", "sample.pdf", mappings)

# Log raw text for debugging
Log("Debug", "Raw extracted text: " + result.rawText)

if result.success then
    Log("Information", "Extracted " + Length(result.kvps) + " values")
else
    Log("Error", "Extraction failed: " + result.error)
end
```

## Notes

- The PDF is streamed directly from blob storage without downloading to disk
- Text extraction works best with text-based PDFs (not scanned images)
- Labels are matched case-sensitively and by exact position in the text
- The stop word prevents extraction from continuing into unwanted sections
- Complex or multi-column layouts may not extract correctly
- For scanned PDFs, consider using OCR services first

## Error Handling

Common errors:
- **Blob does not exist**: Invalid container or path
- **Invalid connection string**: Malformed connection string
- **No valid mappings**: Empty or invalid mappings array
- **PDF parsing errors**: Corrupted or unsupported PDF format

## Related Functions

- [DownloadBlobFiles](../externalblob/DownloadBlobFiles.md) - Download files from external blob storage
- [MoveBlob](../externalblob/MoveBlob.md) - Move processed PDFs
- [GetSecret](../secrets/GetSecret.md) - Retrieve connection strings
- [Notepad](../notepads/CreateNotepad.md) - Store extracted data
