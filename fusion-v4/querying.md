---
layout: default
title: Querying
parent: Globetrotter Fusion 4
nav_order: 40
permalink: /fusion-v4/querying/
---

# Querying
{: .no_toc }

Fusion endpoints that return lists accept a common set of query string parameters for filtering, sorting and paginating the results. Fusion applies them to the response the endpoint would otherwise have returned, so you can ask for exactly the rows you need without the endpoint having to support each query itself.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Query parameters

| Parameter | Aliases | Purpose |
|---|---|---|
| `$filter` | `filter` | Restricts the results to rows that match a boolean expression. |
| `$orderby` | `orderby` | Sorts the results by one or more fields. |
| `$page` | `page` | Selects which page of results to return (1-based). |
| `$pageSize` | `pageSize` | Sets the number of rows per page. |

All four parameters are optional and may be supplied in any combination. Parameter names are matched case-insensitively, and the dollar-prefixed form (e.g. `$filter`) and the bare form (e.g. `filter`) are equivalent.

````url
GET /api/v4/bookings?$filter=isInternational eq true and bookingDate ge '2026-07-01'&$orderby=bookingDate desc&$page=1&$pageSize=100
````

Remember to URL-encode the parameter values: spaces as `%20`, `'` as `%27`, and so on. Most HTTP libraries do this for you. In some shells, `$` must be escaped (`\$filter` in bash, `` `$filter `` in PowerShell).

When no query parameters are supplied, the response is returned unchanged.

## Which responses can be queried

The parameters apply to successful (`200 OK`) JSON responses. They apply to one list in the response:

- If the response is itself an array, they apply to that array.
- If the response is an object, they apply to the array the endpoint designates, or, if it designates none, to the object's first array-valued key. Keys beginning with an underscore are skipped. Globetrotter can tell you which array an endpoint designates.
- If the response has no array, it is returned unchanged.

When the parameters apply to an array inside an object, a `_meta` key is added to the object describing the results. See [Result metadata](#result-metadata).

## Filtering

The `$filter` parameter accepts a boolean expression made of field references, literals, operators and function calls.

### Field references

A field reference identifies a value within a row. References use dot notation to reach into nested objects:

```
field
parent.child
parent.child.grandchild
```

An identifier begins with a letter or underscore and may contain letters, digits and underscores. Field names that contain other characters, or that are [reserved keywords](#reserved-keywords), must be written in bracket notation with a quoted name:

```
customDataFields['Cost Centre']
parent.child['or'].grandchild
```

The quoted name follows the same escaping rules as a string literal.

When any part of a reference is missing or `null`, the whole reference is `null`. See [Null handling](#null-handling) for how operators treat `null`.

### Literals

| Kind | Form |
|---|---|
| String | `'text'`, in single quotes. A single quote inside is written twice: `'O''Brien'`. |
| Number | Integer, decimal or scientific notation, e.g. `42`, `-3.14`, `1.5e-2`. The decimal separator is always `.`. |
| Boolean | `true` or `false` (case-insensitive). |
| Null | `null` (case-insensitive). |

### Comparison operators

| Operator | Meaning |
|---|---|
| `eq` | Equal to |
| `ne` | Not equal to |
| `gt` | Greater than |
| `ge` | Greater than or equal to |
| `lt` | Less than |
| `le` | Less than or equal to |

Comparisons are written `field operator literal`, e.g. `amount gt 100`. The right-hand side must be a literal; comparing two fields is not supported.

How values are compared depends on the literal:

- **Number:** the field is compared as a number. A text field holding a number, e.g. `"42"`, is compared as that number. A field that isn't a number is compared as text.
- **Boolean:** the field is compared as `true` or `false`. A field that is neither matches nothing, except with `ne`.
- **String:** if the field and the literal are both dates or date-times, they are compared in time order, in UTC. Dates and times without a time zone are taken to be UTC. Otherwise, if both the field and the literal hold numbers, e.g. a field of `42` or `"42"` against `'40'`, they are compared as numbers. Otherwise they are compared as text, ignoring case.
- **Null:** only `eq` and `ne` are meaningful. See [Null handling](#null-handling).

For example, `bookingDate ge '2026-07-01'` finds bookings on or after 1 July 2026, and `status eq 'open'` matches `Open` and `OPEN` too.

### Set membership

| Operator | Meaning |
|---|---|
| `in (...)` | The field matches any literal in the list. |
| `nin (...)` | The field matches none of the literals in the list. |

The list is a comma-separated sequence of literals, e.g. `lineType in ('FARE', 'TAX')`. Each is compared with the field as `eq` would. `null` may appear in the list. An empty list, `in ()`, matches no rows, and `nin ()` matches every row.

### String functions

| Function | Meaning |
|---|---|
| `contains(field, 'text')` | The field contains the text. |
| `startswith(field, 'text')` | The field begins with the text. |
| `endswith(field, 'text')` | The field ends with the text. |

All three ignore case. The second argument must be a string literal. A field that isn't text is compared using its text form, and a `null` field matches nothing.

### Logical operators

| Operator | Precedence |
|---|---|
| `not` | Highest: applies to the expression that follows it. |
| `and` | Middle. |
| `or` | Lowest. |

Use parentheses to group expressions and override precedence, e.g. `(status eq 'open' or status eq 'held') and amount gt 0`. Keywords are case-insensitive.

### Null handling

- A field reference that reaches a missing or `null` value is `null`.
- `field eq null` matches rows where the field is `null` or missing; `field ne null` matches rows where it has a value.
- `gt`, `ge`, `lt` and `le` never match `null`, in either the field or the literal.
- When the field is `null` and the literal isn't, only `ne` matches.
- `in (..., null)` matches a `null` field, and `nin (..., null)` excludes it.
- String functions never match a `null` field.

## Sorting

The `$orderby` parameter accepts a comma-separated list of fields, each optionally followed by `asc` (ascending, the default) or `desc` (descending):

```
bookingDate
bookingDate desc
traveller.lastName, traveller.firstName
bookingDate desc, recordLocator
```

Field references use the same dot and bracket notation as filters. The first field is the primary sort, the second breaks ties in the first, and so on.

- Dates and date-times are sorted in time order.
- Numbers, booleans and text are sorted in their natural order. Text is sorted alphabetically, as a dictionary would, so `apple` comes before `Banana`. Where two values differ only in case, lower case comes first.
- `null` sorts last in ascending order, and first in descending order.
- Values of different types in the same field are sorted by type, then by text form.

## Pagination

`$page` selects a page, counting from `1`, and `$pageSize` sets the number of rows per page. Both must be positive whole numbers, no larger than 2,147,483,647.

- `$page` defaults to `1`.
- `$pageSize` defaults to `50`, and is at most `500`: a larger value is reduced to `500`.
- Paging is applied after filtering and sorting.
- A page beyond the last returns no rows, but the result metadata still gives the totals.

To read every row, request pages in turn until a page has fewer rows than `$pageSize`, or until `$page` reaches `_meta.totalPages`. Always include `$orderby` when paging, so that rows don't move between pages.

## Result metadata

When the parameters apply to an array inside an object, a `_meta` key is added to the object, next to the (filtered, sorted and paged) array:

````json
{
  "bookings": [ ... ],
  "_meta": {
    "page": 2,
    "pageSize": 100,
    "total": 734,
    "totalPages": 8,
    "filteredCount": 734
  }
}
````

| Key | When present | Meaning |
|---|---|---|
| `page` | With `$page` or `$pageSize`. | The page returned. |
| `pageSize` | With `$page` or `$pageSize`. | The page size used, after the default and maximum. |
| `total` | With `$filter`, `$page` or `$pageSize`. | The number of rows that matched the filter, before paging. |
| `totalPages` | With `$page` or `$pageSize`. | The number of pages at this page size. |
| `filteredCount` | With `$filter`, `$page` or `$pageSize`. | The same as `total`. |

With only `$orderby`, no `_meta` is added. When the response is itself an array, no `_meta` is added either: count the rows you receive instead.

## Errors

An invalid query parameter returns `400 Bad Request`, and the endpoint isn't queried:

````json
{
  "error": "Invalid $filter",
  "message": "Expected comparison operator after field 'status' at column 7 but found 'is'",
  "parameter": "$filter",
  "input": "status is 'open'"
}
````

| Key | Meaning |
|---|---|
| `error` | Which parameter is invalid. |
| `message` | What is wrong, and where. Column positions count from 0. |
| `parameter` | The parameter, e.g. `$filter`. |
| `input` | The parameter's value, as Fusion received it. |

Conditions that make a parameter invalid include:

- an unrecognised character or word
- a string literal with no closing quote
- a number that isn't valid
- a missing or malformed field reference, or an unquoted name inside brackets
- a comparison, `in` or string function with a missing or wrong-typed argument
- unbalanced parentheses or brackets
- something unexpected at the end
- a `$page` or `$pageSize` that isn't a positive whole number
- an `$orderby` direction other than `asc` or `desc`.

An empty `$filter` or `$orderby`, e.g. `$filter=`, is ignored, as if it weren't sent.

The `parameter` key always uses the `$` form, e.g. `$filter`, even when the request used `filter`.

## Reserved keywords

These words are part of the filter language, and can't be used as bare field names. If a response uses one as a key, use bracket notation, e.g. `['in']`:

```
eq  ne  gt  ge  lt  le
in  nin
and  or  not
true  false  null
contains  startswith  endswith
```

Keywords are matched without regard to case. `asc` and `desc` are not reserved: they are recognised only after a field in `$orderby`, so `$orderby=desc desc` sorts a field named `desc` in descending order.
