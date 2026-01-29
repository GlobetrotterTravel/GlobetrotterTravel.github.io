---
layout: default
title: SqlQuery
parent: Iris API functions
has_children: false
has_toc: false
---

# SqlQuery

Executes a SQL SELECT query and returns results as an array of arrays.

## Syntax

```jyro
SqlQuery(connectionName, query)
SqlQuery(connectionName, query, parameters)
```

## Parameters

- **connectionName** (string): Name of a connection established via `SqlConnect()`
- **query** (string): The SQL query to execute (typically SELECT)
- **parameters** (object, optional): Object with parameter values for parameterized queries

## Returns

- **array**: Array of arrays where:
  - First row contains column names (headers)
  - Subsequent rows contain data values
  - Empty array if query fails or returns no results

## Description

Executes a SQL query against an established connection and returns results in a tabular format. The first array element contains column headers, making it easy to process results in a loop.

Supports parameterized queries to prevent SQL injection attacks. Parameter names in the query should be prefixed with `@` (e.g., `@customerId`).

### Safety Limits

- Maximum 10,000 rows returned per query
- 30-second query timeout
- Results truncated with a warning if limits exceeded

## Examples

### Simple query

```jyro
SqlConnect("myDb", "DB_CONNECTION_SECRET")

var results = SqlQuery("myDb", "SELECT Id, Name, Email FROM Customers")

# results = [["Id", "Name", "Email"], [1, "John", "john@example.com"], ...]

if Length(results) > 1 then
    var headers = results[0]
    Log("Information", "Columns: " + headers[0] + ", " + headers[1] + ", " + headers[2])

    # Process data rows (skip header)
    foreach row in Slice(results, 1) do
        Log("Information", "Customer: " + row[1] + " (" + row[2] + ")")
    end
else
    Log("Warning", "No customers found")
end
```

### Parameterized query

```jyro
SqlConnect("myDb", "DB_CONNECTION_SECRET")

# Use parameters to prevent SQL injection
var results = SqlQuery("myDb",
    "SELECT * FROM Orders WHERE CustomerId = @custId AND Status = @status",
    { custId: 123, status: "Active" })

foreach row in Slice(results, 1) do
    Log("Information", "Order ID: " + row[0])
end
```

### Query with date parameters

```jyro
SqlConnect("reportDb", "REPORT_DB_CONNECTION")

var startDate = "2024-01-01"
var endDate = "2024-12-31"

var results = SqlQuery("reportDb",
    "SELECT OrderDate, Total FROM Orders WHERE OrderDate BETWEEN @start AND @end",
    { start: startDate, end: endDate })

var totalSales = 0
foreach row in Slice(results, 1) do
    totalSales = totalSales + row[1]
end

Log("Information", "Total sales for period: " + totalSales)
```

### Processing results into objects

```jyro
SqlConnect("myDb", "DB_CONNECTION_SECRET")

var results = SqlQuery("myDb", "SELECT Id, FirstName, LastName, Email FROM Users WHERE Active = 1")

if Length(results) <= 1 then
    Log("Warning", "No active users found")
    return
end

var headers = results[0]
var users = []

foreach row in Slice(results, 1) do
    var user = {
        id: row[0],
        firstName: row[1],
        lastName: row[2],
        email: row[3]
    }
    users = Append(users, user)
end

Log("Information", "Loaded " + Length(users) + " users")

# Now work with user objects
foreach user in users do
    Log("Debug", user.firstName + " " + user.lastName)
end
```

### Checking for empty results

```jyro
SqlConnect("myDb", "DB_CONNECTION_SECRET")

var results = SqlQuery("myDb",
    "SELECT * FROM Products WHERE Category = @cat",
    { cat: "Electronics" })

# Results always include header row, so check for > 1
if Length(results) <= 1 then
    Log("Information", "No products found in category")
    Data._payload = { products: [], count: 0 }
else
    var productCount = Length(results) - 1
    Log("Information", "Found " + productCount + " products")
    Data._payload = { products: Slice(results, 1), count: productCount }
end
```

## Data Type Conversion

SQL data types are automatically converted:

| SQL Type | Jyro Type |
|----------|-----------|
| INT, BIGINT | number |
| FLOAT, DECIMAL | number |
| VARCHAR, NVARCHAR | string |
| BIT | boolean |
| DATETIME, DATETIME2 | string (ISO 8601) |
| UNIQUEIDENTIFIER | string |
| VARBINARY | string (base64) |
| NULL | null |

## Error Handling

The function returns an empty array on error. Always check the result length before processing:

```jyro
var results = SqlQuery("myDb", "SELECT * FROM NonExistentTable")

if Length(results) == 0 then
    Log("Error", "Query failed or returned no results")
end
```

## Notes

- Always use parameterized queries for user-provided values
- Connection must be established with `SqlConnect` before querying
- Large result sets are automatically truncated at 10,000 rows
- Query execution times out after 30 seconds

## Related Functions

- [SqlConnect](SqlConnect.md) - Establish a database connection
- [SqlExecute](SqlExecute.md) - Execute non-query commands
- [SqlClose](SqlClose.md) - Close a connection
