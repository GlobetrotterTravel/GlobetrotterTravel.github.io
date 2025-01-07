# Bulk Uploads

The **Bulk Upload API** enables administrators to efficiently manage large datasets by uploading CSV files. This endpoint supports various operations, including inserting new records, updating existing ones, and synchronizing data by replacing the entire dataset. The behavior of the upload process is controlled using the `mode` parameter, allowing precise management of how the data interacts with existing records.

## Endpoint

### URL

```
POST /bulk-upload
```

### Method

```
POST
```

### Headers

The request must include the `Content-Type` header set to `multipart/form-data`. Additionally, an `Authorization` header containing a valid bearer token is required for authenticated access.

## Request Structure

To perform a bulk upload, the request should be constructed as `multipart/form-data`, including a CSV file and the `mode` parameter. The CSV file is submitted with the field name `file` and must contain a unique identifier for each record, such as `id` or `email`. The `mode` parameter, which dictates the behavior of the upload process, should be included either as a query parameter in the URL or within the request body.

### Example Request

```http
POST /bulk-upload?mode=upsert
Content-Type: multipart/form-data
Authorization: Bearer your_token_here

--boundary
Content-Disposition: form-data; name="file"; filename="data.csv"
Content-Type: text/csv

id,name,email
1,John Doe,john@example.com
2,Jane Smith,jane@example.com
--boundary--
```

## Mode Parameter

The `mode` parameter is essential in defining how the bulk upload interacts with existing data. It accepts four values: `insert`, `upsert`, `update`, and `replace`, each serving a distinct purpose.

- **Insert (`insert`)**: This mode is used to add new records to the database. If a record with the same unique identifier already exists, the operation for that specific record will either fail or be ignored, depending on the system's configuration. This mode is ideal when uploading entirely new data without modifying existing records. Administrators should ensure that the CSV contains only unique, non-existing records to prevent partial failures.

- **Upsert (`upsert`)**: This mode combines insertion and updating functionalities. New records are added to the database, while existing records identified by their unique identifiers are updated with the provided data. This mode is suitable for scenarios where administrators need to add new entries and modify existing ones simultaneously. It is important to verify that the CSV includes all necessary fields for both insertion and update operations to avoid partial updates.

- **Update (`update`)**: This mode is designed to update only existing records in the database. Records present in the CSV are updated, while those not included remain unchanged. If the CSV contains records that do not match any existing records, those entries will either fail or be ignored based on system settings. Administrators should ensure that all records intended for update are accurately identified in the CSV to prevent unintended failures.

- **Replace (`replace`)**: This mode synchronizes the server data with the CSV by inserting new records, updating existing ones, and deleting any records in the database that are not present in the CSV. This operation is destructive as it removes data not included in the upload, making it crucial to ensure that the CSV accurately represents the desired final state of the data.

## CSV File Requirements

The CSV file must include a unique identifier for each record to facilitate accurate insertion and updating. All mandatory fields required by the server should be present and correctly formatted. The CSV should adhere to standard formatting practices, including proper escaping of special characters and consistent delimiter usage. Additionally, the first row must contain headers that precisely match the expected field names on the server, as field names are case-sensitive.

## Response Structure

Upon completing the bulk upload, the API returns a JSON response detailing the outcome of the operation. The response includes the mode used, the number of records inserted, updated, deleted (if applicable), and any failures encountered during processing. A timestamp indicating when the operation was completed is also provided.

### Success Response Example

```json
{
  "mode": "upsert",
  "inserted": 150,
  "updated": 75,
  "deleted": 0,
  "failed": [
    {
      "row": 10,
      "error": "Duplicate entry for unique field 'email'."
    },
    {
      "row": 45,
      "error": "Missing required field 'name'."
    }
  ],
  "timestamp": "2024-11-08T12:34:56Z"
}
```

### Error Handling

Errors encountered during the bulk upload process are communicated through appropriate HTTP status codes and detailed messages in the response body. Common status codes include:

- **200 OK**: The request was successfully processed, with any partial successes or failures detailed in the response.
- **400 Bad Request**: The request was malformed, such as missing required parameters or an improperly formatted CSV.
- **401 Unauthorized**: Authentication credentials were missing or invalid.
- **403 Forbidden**: The user lacks permission to perform the bulk upload.
- **500 Internal Server Error**: An unexpected error occurred on the server.

#### Error Response Example

```json
{
  "error": {
    "code": 400,
    "message": "Invalid CSV format: Missing header 'email'.",
    "details": [
      {
        "row": 1,
        "error": "Missing 'email' field."
      }
    ]
  },
  "timestamp": "2024-11-08T12:34:56Z"
}
```

## Security and Permissions

Bulk uploads require authenticated access. Users must include valid authentication tokens in the `Authorization` header. Additionally, users must possess the necessary permissions to execute the specified mode. For instance, performing delete operations in `replace` mode may require elevated privileges. It is essential to ensure that only authorized personnel can perform bulk uploads to maintain data integrity and security.

## Transaction Management

The bulk upload operation may be configured to be atomic, meaning that if any part of the upload fails, the entire operation is rolled back to maintain data integrity. In configurations where atomicity is not enforced, some records may be processed successfully while others fail. The response will indicate the number of successful operations and provide detailed information about any failures, allowing administrators to take corrective actions as needed.

## Caveats and Non-Intuitive Behaviors

Administrators should be aware of several caveats when using the Bulk Upload API:

- **Duplicate Records in CSV**: Uploading a CSV containing duplicate unique identifiers can lead to unexpected failures or ignored records, especially in `insert` and `update` modes. It is crucial to ensure that each unique identifier appears only once in the CSV.

- **Delete Operations in Replace Mode**: Using the `replace` mode will delete any records not present in the CSV. Administrators must ensure that the CSV includes all records intended to remain in the database to prevent accidental data loss.

- **Large File Sizes**: Uploading extremely large CSV files may result in longer processing times or timeouts. It is advisable to consult system limitations and consider breaking down large datasets into smaller batches if necessary.

- **Field Validation**: Strict validation is performed on each record. Records with invalid data formats or missing required fields will fail, and detailed error messages will be provided to assist in troubleshooting.

- **Case Sensitivity**: Field names in the CSV header are case-sensitive and must match the expected names exactly. Any discrepancies can lead to failures in processing the records.

## Examples

### Insert Only New Records

**Request:**

```http
POST /bulk-upload?mode=insert
Content-Type: multipart/form-data
Authorization: Bearer your_token_here

--boundary
Content-Disposition: form-data; name="file"; filename="new_records.csv"
Content-Type: text/csv

id,name,email
3,Alice Johnson,alice@example.com
4,Bob Lee,bob@example.com
--boundary--
```

**Response:**

```json
{
  "mode": "insert",
  "inserted": 2,
  "updated": 0,
  "deleted": 0,
  "failed": [],
  "timestamp": "2024-11-08T12:34:56Z"
}
```

### Upsert (Insert and Update)

**Request:**

```http
POST /bulk-upload?mode=upsert
Content-Type: multipart/form-data
Authorization: Bearer your_token_here

--boundary
Content-Disposition: form-data; name="file"; filename="upsert_records.csv"
Content-Type: text/csv

id,name,email
1,John Doe Updated,john.doe@example.com
5,Charlie Kim,charlie@example.com
--boundary--
```

**Response:**

```json
{
  "mode": "upsert",
  "inserted": 1,
  "updated": 1,
  "deleted": 0,
  "failed": [],
  "timestamp": "2024-11-08T12:35:56Z"
}
```

### Update Only Existing Records

**Request:**

```http
POST /bulk-upload?mode=update
Content-Type: multipart/form-data
Authorization: Bearer your_token_here

--boundary
Content-Disposition: form-data; name="file"; filename="update_records.csv"
Content-Type: text/csv

id,name,email
2,Jane Smith Updated,jane.smith@example.com
6,David Park,david@example.com
--boundary--
```

**Response:**

```json
{
  "mode": "update",
  "inserted": 0,
  "updated": 1,
  "deleted": 0,
  "failed": [
    {
      "row": 2,
      "error": "Record with id '6' does not exist."
    }
  ],
  "timestamp": "2024-11-08T12:36:56Z"
}
```

### Replace Entire Dataset

**Request:**

```http
POST /bulk-upload?mode=replace
Content-Type: multipart/form-data
Authorization: Bearer your_token_here

--boundary
Content-Disposition: form-data; name="file"; filename="replace_records.csv"
Content-Type: text/csv

id,name,email
1,John Doe,john@example.com
3,Alice Johnson,alice@example.com
--boundary--
```

**Response:**

```json
{
  "mode": "replace",
  "inserted": 0,
  "updated": 2,
  "deleted": 1,
  "failed": [],
  "timestamp": "2024-11-08T12:37:56Z"
}
```

## Best Practices

Administrators should follow several best practices to ensure successful bulk uploads:

1. **Backup Data**: Before performing operations that modify or delete data, especially using the `replace` mode, ensure that backups are available to prevent accidental data loss.

2. **Validate CSVs**: Prior to uploading, validate the CSV file for correct formatting, required fields, and unique identifiers to minimize errors during processing.

3. **Monitor Responses**: Always review the API response to identify any failed records and take corrective actions as needed. Detailed error messages assist in troubleshooting specific issues.

4. **Incremental Uploads**: For very large datasets, consider breaking the upload into smaller, incremental batches. This approach helps manage processing times and reduces the risk of timeouts.

## Support and Versioning

Administrators should reference the correct API version to ensure compatibility, as changes to the bulk upload functionality may be versioned separately. For assistance or to report issues, contact the support team or refer to the developer community forums.

## Conclusion

The Bulk Upload API provides a robust and flexible solution for managing large datasets efficiently. By selecting the appropriate `mode` and adhering to the CSV requirements, administrators can seamlessly insert, update, or synchronize their data. Understanding the outlined caveats and following best practices will ensure a smooth and reliable upload process, maintaining data integrity and operational efficiency.