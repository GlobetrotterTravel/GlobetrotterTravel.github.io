---
layout: default
title: GenerateExcelBytes
parent: Iris API functions
has_children: false
has_toc: false
---

# GenerateExcelBytes

Generates an Excel workbook and returns raw bytes, allowing scripts to persist the file to blob storage or process it further.

## Syntax

```jyro
GenerateExcelBytes(worksheets)
```

## Parameters

- **worksheets** (array): Array of worksheet definitions, each containing:
  - **name** (string): Worksheet name
  - **data** (array): Array of row objects (each object's keys become column headers)
  - **tableName** (string, optional): Excel table name for formatting

## Returns

- **array**: Byte array of the Excel file (.xlsx format), or `null` on failure

## Description

This is a companion function to `GenerateExcel()`. While `GenerateExcel()` returns a temporary download URL (1-hour expiry), `GenerateExcelBytes()` returns the raw bytes of the Excel file. This allows scripts to:

- Persist the file to blob storage for long-term access
- Process the bytes further before storage
- Control exactly where and how the file is stored

The generated Excel file:
- Uses .xlsx format (Office Open XML)
- Auto-sizes columns to fit content
- Applies table formatting when `tableName` is specified
- Handles various data types (strings, numbers, dates, booleans)

## Examples

### Basic Excel Generation with Blob Storage

```jyro
# Define worksheet data
var customerData = []
Append(customerData, { "CustomerNo": "C001", "Name": "Acme Corp", "Active": true })
Append(customerData, { "CustomerNo": "C002", "Name": "Globex Inc", "Active": true })
Append(customerData, { "CustomerNo": "C003", "Name": "Initech", "Active": false })

var worksheets = [
    {
        "name": "Customers",
        "data": customerData,
        "tableName": "CustomerTable"
    }
]

# Generate Excel bytes
var excelBytes = GenerateExcelBytes(worksheets)

if excelBytes != null then
    # Upload to blob storage
    var fileName = "customers_" + FormatDate(Now(), "yyyyMMdd_HHmmss") + ".xlsx"
    var success = WriteToBlobStorage("exports", fileName, excelBytes)

    if success then
        # Generate download URL valid for 24 hours
        var downloadUrl = GetBlobStorageSasUrl("exports", fileName, 1440)

        Data._payload = {
            "success": true,
            "downloadUrl": downloadUrl,
            "fileName": fileName
        }
        Data._statusCode = 200
    else
        Data._payload = {}
        Data._payload.error = "Failed to upload file"
        Data._statusCode = 500
    end
else
    Data._payload = {}
    Data._payload.error = "Failed to generate Excel"
    Data._statusCode = 500
end
```

### Multiple Worksheets

```jyro
# Build summary data
var summaryData = []
Append(summaryData, { "Metric": "Total Orders", "Value": Length(orders) })
Append(summaryData, { "Metric": "Total Lines", "Value": Length(orderLines) })
Append(summaryData, { "Metric": "Generated", "Value": Now() })

var worksheets = [
    {
        "name": "Orders",
        "data": orders,
        "tableName": "OrdersTable"
    },
    {
        "name": "Order Lines",
        "data": orderLines,
        "tableName": "LinesTable"
    },
    {
        "name": "Summary",
        "data": summaryData
    }
]

var excelBytes = GenerateExcelBytes(worksheets)
WriteToBlobStorage("reports/orders", "order_report.xlsx", excelBytes)
```

### Smart Export with Fallback

```jyro
# Generate Excel bytes
var excelBytes = GenerateExcelBytes(worksheets)

if IsBlobStorageEnabled() then
    # Persist to blob storage
    var fileName = "report_" + FormatDate(Now(), "yyyyMMdd") + ".xlsx"
    WriteToBlobStorage("exports", fileName, excelBytes)
    var downloadUrl = GetBlobStorageSasUrl("exports", fileName, 1440)

    Data._payload = {
        "downloadUrl": downloadUrl,
        "persistent": true,
        "expiresIn": "24 hours (link only - file persists)"
    }
else
    # Fall back to temporary in-memory storage
    var downloadUrl = GenerateExcel(worksheets, "report.xlsx")

    Data._payload = {
        "downloadUrl": downloadUrl,
        "persistent": false,
        "expiresIn": "1 hour"
    }
end

Data._statusCode = 200
```

### Scheduled Nightly Export

```jyro
# ETL Script: nightly-export
# Runs on schedule to export data to blob storage

# Fetch data from external API
var response = FetchExternalData("https://api.example.com/data")
var data = response.data

# Create worksheets
var worksheets = [
    {
        "name": "Data Export",
        "data": data,
        "tableName": "ExportTable"
    }
]

# Generate and persist
var excelBytes = GenerateExcelBytes(worksheets)
var fileName = "EXPORT_" + FormatDate(Now(), "ddMMyyyy_HHmmss") + ".xlsx"
var blobPath = "scheduled-exports/" + FormatDate(Now(), "yyyy/MM")

var uploaded = WriteToBlobStorage(blobPath, fileName, excelBytes)

if uploaded then
    Log("Information", "Nightly export completed: " + blobPath + "/" + fileName)

    # Generate link for email notification (valid 7 days)
    var downloadUrl = GetBlobStorageSasUrl(blobPath, fileName, 10080)

    # Send email notification with template data
    var templateData = {
        "fileName": fileName,
        "downloadUrl": downloadUrl,
        "exportedAt": Now()
    }
    SendEmail(
        "reports@company.com",
        "Nightly Export Ready",
        RenderTemplate("export-ready", templateData)
    )
else
    Log("Error", "Nightly export failed for " + fileName)
end
```

## Comparison with GenerateExcel

| Feature | GenerateExcel | GenerateExcelBytes |
|---------|---------------|-------------------|
| Returns | Download URL (string) | Byte array |
| Storage | In-memory cache | None (you control) |
| Expiry | 1 hour | N/A (no auto-storage) |
| Use Case | Quick downloads | Persistent storage |
| Flexibility | Limited | Full control |

## Notes

- Returns `null` if worksheet array is empty or invalid
- Each worksheet must have a `name` and `data` property
- Column headers are derived from the keys of the first data object
- All rows should have consistent keys for best results
- Large exports may take longer to generate - consider execution timeouts
- The byte array can be large for big datasets - monitor memory usage

## See Also

- [GenerateExcel](../GenerateExcel.md) - Generate Excel with temporary download URL
- [WriteToBlobStorage](WriteToBlobStorage.md) - Upload bytes to blob storage
- [GetBlobStorageSasUrl](GetBlobStorageSasUrl.md) - Generate download URLs
