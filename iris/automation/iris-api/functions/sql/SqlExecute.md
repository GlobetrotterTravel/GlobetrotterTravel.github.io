---
layout: default
title: SqlExecute
parent: Iris API functions
has_children: false
has_toc: false
---

# SqlExecute

Executes a SQL command that doesn't return a result set (INSERT, UPDATE, DELETE, etc.).

## Syntax

```jyro
SqlExecute(connectionName, command)
SqlExecute(connectionName, command, parameters)
```

## Parameters

- **connectionName** (string): Name of a connection established via `SqlConnect()`
- **command** (string): The SQL command to execute (INSERT, UPDATE, DELETE, etc.)
- **parameters** (object, optional): Object with parameter values for parameterized commands

## Returns

- **number**: Number of rows affected by the command, or `-1` on error

## Description

Executes SQL commands that modify data rather than retrieve it. Returns the count of affected rows, which is useful for verifying that operations completed as expected.

Supports parameterized commands to prevent SQL injection attacks. Always use parameters for any user-provided or dynamic values.

### Safety Limits

- 30-second command timeout

## Examples

### Insert a record

```jyro
SqlConnect("myDb", "DB_CONNECTION_SECRET")

var inserted = SqlExecute("myDb",
    "INSERT INTO Logs (Message, CreatedAt, Level) VALUES (@msg, @ts, @level)",
    { msg: "Script executed successfully", ts: Now(), level: "Information" })

if inserted > 0 then
    Log("Information", "Log entry created")
else
    Log("Warning", "Failed to create log entry")
end
```

### Update records

```jyro
SqlConnect("myDb", "DB_CONNECTION_SECRET")

var updated = SqlExecute("myDb",
    "UPDATE Customers SET LastContactDate = @date WHERE Region = @region",
    { date: Now(), region: "West" })

Log("Information", "Updated " + updated + " customer(s)")
```

### Delete records

```jyro
SqlConnect("myDb", "DB_CONNECTION_SECRET")

var deleted = SqlExecute("myDb",
    "DELETE FROM TempData WHERE ExpiresAt < @now",
    { now: Now() })

Log("Information", "Cleaned up " + deleted + " expired record(s)")
```

### Bulk operations

```jyro
SqlConnect("myDb", "DB_CONNECTION_SECRET")

# Process a list of items
var items = Data.itemsToProcess
var successCount = 0
var failCount = 0

foreach item in items do
    var result = SqlExecute("myDb",
        "UPDATE Inventory SET Quantity = Quantity - @qty WHERE ProductId = @id AND Quantity >= @qty",
        { id: item.productId, qty: item.quantity })

    if result > 0 then
        successCount = successCount + 1
    else
        failCount = failCount + 1
        Log("Warning", "Failed to update inventory for product " + item.productId)
    end
end

Log("Information", "Processed " + successCount + " items, " + failCount + " failed")
```

### Execute without parameters

```jyro
SqlConnect("myDb", "DB_CONNECTION_SECRET")

# Be careful with commands that don't use parameters
var affected = SqlExecute("myDb", "TRUNCATE TABLE TempLogs")

if affected >= 0 then
    Log("Information", "Temporary logs cleared")
end
```

### Transaction-like behavior with verification

```jyro
SqlConnect("orderDb", "ORDER_DB_CONNECTION")

var orderId = Data.orderId
var newStatus = "Shipped"

# Update order status
var updated = SqlExecute("orderDb",
    "UPDATE Orders SET Status = @status, ShippedDate = @date WHERE Id = @id AND Status = 'Processing'",
    { id: orderId, status: newStatus, date: Now() })

if updated == 1 then
    # Insert audit record
    SqlExecute("orderDb",
        "INSERT INTO OrderAudit (OrderId, Action, ActionDate, UserId) VALUES (@orderId, @action, @date, @userId)",
        { orderId: orderId, action: "StatusChanged:" + newStatus, date: Now(), userId: GetCurrentUser().id })

    Log("Information", "Order " + orderId + " marked as shipped")
else
    Log("Warning", "Order " + orderId + " was not updated - may already be shipped or not found")
end
```

### Creating records and getting identity

```jyro
SqlConnect("myDb", "DB_CONNECTION_SECRET")

# Insert and get the new ID in one operation
var results = SqlQuery("myDb",
    "INSERT INTO Products (Name, Price) OUTPUT INSERTED.Id VALUES (@name, @price)",
    { name: "New Product", price: 29.99 })

if Length(results) > 1 then
    var newProductId = results[1][0]
    Log("Information", "Created product with ID: " + newProductId)
end
```

## Error Handling

Returns `-1` when an error occurs:

```jyro
var result = SqlExecute("myDb", "UPDATE NonExistentTable SET X = 1")

if result < 0 then
    Log("Error", "Command execution failed")
end
```

Common error scenarios:
- Connection not established
- Invalid SQL syntax
- Constraint violations (foreign key, unique, etc.)
- Permission denied
- Query timeout

## Notes

- Always use parameterized commands for dynamic values
- Connection must be established with `SqlConnect` before executing
- Check the return value to verify the expected number of rows were affected
- Commands time out after 30 seconds
- For commands that return data (like INSERT with OUTPUT), use `SqlQuery` instead

## Related Functions

- [SqlConnect](SqlConnect) - Establish a database connection
- [SqlQuery](SqlQuery) - Execute SELECT queries
- [SqlClose](SqlClose) - Close a connection
