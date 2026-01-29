---
layout: default
title: SqlClose
parent: Iris API functions
has_children: false
has_toc: false
---

# SqlClose

Explicitly closes a SQL database connection.

## Syntax

```jyro
SqlClose(connectionName)
```

## Parameters

- **connectionName** (string): Name of the connection to close

## Returns

- **boolean**: `true` if connection was closed, `false` if connection was not found

## Description

Closes a SQL Server connection that was previously established with `SqlConnect`. This is useful for freeing up resources early in long-running scripts or when you need to reconnect with different credentials.

Connections are automatically closed when script execution ends, so calling `SqlClose` is optional but recommended for resource-conscious scripts.

## Examples

### Close connection early

```jyro
SqlConnect("tempDb", "TEMP_DB_CONNECTION")

# Run a quick query
var data = SqlQuery("tempDb", "SELECT * FROM TempTable")

# Close immediately to free resources
SqlClose("tempDb")

# Continue with other work that doesn't need the database
foreach row in Slice(data, 1) do
    # Process data...
end

Log("Information", "Processed " + (Length(data) - 1) + " records")
```

### Verify connection closed

```jyro
SqlConnect("myDb", "DB_CONNECTION_SECRET")

# Do work...
var results = SqlQuery("myDb", "SELECT COUNT(*) FROM Users")

# Close and verify
var closed = SqlClose("myDb")

if closed then
    Log("Information", "Database connection closed successfully")
else
    Log("Warning", "Connection was not found (already closed?)")
end
```

### Multiple connections with selective closing

```jyro
# Connect to multiple databases
SqlConnect("sourceDb", "SOURCE_DB_CONNECTION")
SqlConnect("targetDb", "TARGET_DB_CONNECTION")

# Read from source
var sourceData = SqlQuery("sourceDb", "SELECT * FROM DataToMigrate WHERE Processed = 0")

# Done with source, close it
SqlClose("sourceDb")

# Write to target (might take a while)
foreach row in Slice(sourceData, 1) do
    SqlExecute("targetDb",
        "INSERT INTO MigratedData (Id, Value) VALUES (@id, @value)",
        { id: row[0], value: row[1] })
end

# Close target when done
SqlClose("targetDb")

Log("Information", "Migration complete")
```

### Reconnecting with different credentials

```jyro
# Connect as read-only user
SqlConnect("db", "DB_READONLY_CONNECTION")
var data = SqlQuery("db", "SELECT * FROM SensitiveTable")
SqlClose("db")

# Reconnect as admin for write operations
SqlConnect("db", "DB_ADMIN_CONNECTION")
SqlExecute("db", "UPDATE AuditLog SET Accessed = @date WHERE TableName = 'SensitiveTable'", { date: Now() })
SqlClose("db")
```

## Notes

- Closing a connection that doesn't exist returns `false` but doesn't cause an error
- After closing, the connection name can be reused with `SqlConnect`
- All connections are automatically closed when the script ends
- Connection names are case-sensitive

## Related Functions

- [SqlConnect](SqlConnect.md) - Establish a database connection
- [SqlQuery](SqlQuery.md) - Execute SELECT queries
- [SqlExecute](SqlExecute.md) - Execute non-query commands
