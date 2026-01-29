---
layout: default
title: SqlConnect
parent: Iris API functions
has_children: false
has_toc: false
---

# SqlConnect

Establishes a connection to a SQL Server database using a connection string stored as a secret.

## Syntax

```jyro
SqlConnect(connectionName, secretName)
```

## Parameters

- **connectionName** (string): A unique name to reference this connection in subsequent queries
- **secretName** (string): The name of the secret containing the SQL Server connection string

## Returns

- **boolean**: `true` if connection was established successfully, `false` on failure

## Description

Establishes a SQL Server connection using credentials stored securely in the Secrets service. The connection string is retrieved from the named secret and registered for use in subsequent `SqlQuery` and `SqlExecute` calls.

Multiple connections can be established in a single script by using different connection names. Connections are automatically closed when script execution ends, but can be explicitly closed early using `SqlClose`.

## Examples

### Basic connection

```jyro
# Establish connection using a secret
var connected = SqlConnect("salesDb", "SALES_DB_CONNECTION")

if not connected then
    Log("Error", "Failed to connect to sales database")
    return
end

# Now use the connection for queries
var customers = SqlQuery("salesDb", "SELECT TOP 10 * FROM Customers")
```

### Multiple database connections

```jyro
# Connect to multiple databases
var salesConnected = SqlConnect("salesDb", "SALES_DB_CONNECTION")
var inventoryConnected = SqlConnect("inventoryDb", "INVENTORY_DB_CONNECTION")

if not salesConnected or not inventoryConnected then
    Log("Error", "Failed to connect to one or more databases")
    return
end

# Query from both databases
var orders = SqlQuery("salesDb", "SELECT * FROM Orders WHERE Status = 'Pending'")
var stock = SqlQuery("inventoryDb", "SELECT * FROM Stock WHERE Quantity < 10")

# Process results...
```

### Connection with error handling

```jyro
# Ensure the secret exists first
var connString = GetSecret("REPORTING_DB_CONNECTION")
if connString == null then
    Log("Error", "Database connection secret not configured")
    CancelAction("Missing database configuration")
    return
end

# Establish connection
if SqlConnect("reportingDb", "REPORTING_DB_CONNECTION") then
    Log("Information", "Connected to reporting database")

    # Run queries...
    var results = SqlQuery("reportingDb", "SELECT COUNT(*) as Total FROM Reports")

    # Clean up when done
    SqlClose("reportingDb")
else
    Log("Error", "Connection failed - check secret value")
end
```

## Connection String Format

The secret should contain a standard SQL Server connection string:

```
Server=myserver.database.windows.net;Database=mydb;User Id=myuser;Password=mypassword;
```

For Azure SQL with managed identity:

```
Server=myserver.database.windows.net;Database=mydb;Authentication=Active Directory Default;
```

## Notes

- Connection strings are retrieved from the Secrets service for security
- Use `SetSecret` to store connection strings securely before use
- Connections are pooled and reused where possible
- Connection names are case-sensitive
- If a connection with the same name already exists, it will be replaced

## Related Functions

- [SqlQuery](SqlQuery) - Execute SELECT queries
- [SqlExecute](SqlExecute) - Execute INSERT/UPDATE/DELETE commands
- [SqlClose](SqlClose) - Close a connection
- [GetSecret](../secrets/GetSecret) - Retrieve secret values
- [SetSecret](../secrets/SetSecret) - Store secret values
