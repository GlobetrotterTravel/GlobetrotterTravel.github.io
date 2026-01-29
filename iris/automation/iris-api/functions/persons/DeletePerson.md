---
layout: default
title: DeletePerson
parent: Iris API functions
has_children: false
has_toc: false
---

# DeletePerson

Permanently deletes a person from the database.

## Syntax

```jyro
DeletePerson(personId)
```

## Parameters

- **personId** (string): The GUID string of the person to delete

## Returns

- **boolean**: true if the deletion was successful, false otherwise

## Description

Performs a hard delete of a person record from the database. This operation is permanent and cannot be undone. The function returns false if the person does not exist or if the deletion fails for any reason.

**Warning**: This is a destructive operation. Consider deactivating persons instead of deleting them if you need to maintain historical records.

## Examples

```jyro
# Delete person by ID
var success = DeletePerson("12345678-1234-1234-1234-123456789abc")
if success then
    Log("Information", "Person deleted successfully")
end
```

```jyro
# Validate before deleting
var person = GetPersonById(personId)
if person is not null then
    var deleted = DeletePerson(personId)
    if not deleted then
        Log("Warning", "Failed to delete person")
    end
end
```

```jyro
# Delete with error handling in Before hook
var deleted = DeletePerson(personId)
if not deleted then
    CancelAction("Failed to delete person")
end
```

```jyro
# Delete person and notify admin
var person = GetPersonById(personId)
if person is not null then
    var email = person.emailAddress
    var name = person.firstName + " " + person.lastName

    var deleted = DeletePerson(personId)
    if deleted then
        SendNotification(
            "admin@company.com",
            "Person Deleted",
            "Person was deleted: " + name + " (" + email + ")",
            "Warning",
            null
        )
        Log("Information", "Deleted person: " + email)
    else
        Log("Error", "Failed to delete person: " + email)
    end
end
```

```jyro
# Bulk delete inactive persons (use with caution)
var persons = GetAllPersons()
var deletedCount = 0

foreach person in persons do
    if not person.isActive then
        var deleted = DeletePerson(person.id)
        if deleted then
            deletedCount = deletedCount + 1
        end
    end
end

Log("Information", "Deleted " + deletedCount + " inactive persons")
```

## Related Functions

- [GetPersonById](GetPersonById.md) - Retrieve a person by ID
- [UpdatePerson](UpdatePerson.md) - Update a person (consider deactivating instead of deleting)
- [CreatePerson](CreatePerson.md) - Create a new person
