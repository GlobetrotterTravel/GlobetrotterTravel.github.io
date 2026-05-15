---
layout: default
title: Querying
parent: Globetrotter Fusion
nav_order: 30
permalink: /fusion/querying/
---

# Querying
{: .no_toc }

Fusion endpoints that return list-shaped payloads accept a common set of query-string parameters for filtering, sorting, and paginating results. The query is parsed and evaluated server-side against the payload that the endpoint would otherwise have returned.

## Table of contents
{: .no_toc }

1. TOC
{:toc }

## Query parameters

| Parameter | Aliases | Purpose |
|---|---|---|
| `filter` | `$filter` | Restricts the result set to rows that match a boolean expression. |
| `orderby` | `$orderby` | Sorts the result set by one or more fields. |
| `page` | `$page` | Selects which page of results to return (1-based). |
| `pageSize` | `$pageSize` | Sets the number of rows per page. |

All four parameters are optional and may be supplied in any combination. Parameter names are matched case-insensitively, and either the bare form (e.g. `filter`) or the dollar-prefixed form (e.g. `$filter`) is accepted; the two forms are equivalent.

When no query parameters are supplied the payload is returned unchanged.

## Application to payloads

The query is applied to a single list within the payload:

- If the payload is itself an array, the query is applied to that array.
- If the payload is an object, the query is applied to the array-valued property identified by the endpoint's query target. If no target is configured, the first non-metadata property whose value is an array is used. Property names beginning with an underscore are treated as metadata and skipped during this fallback selection.
- If no suitable array can be located, the payload is returned unchanged.

When the query is applied to an array inside an object payload, a `_meta` property is added to the object describing the result set. See [Result metadata](#result-metadata).

## Filtering

The `filter` parameter accepts a boolean expression composed of field references, literals, operators, and function calls.

### Field references

A field reference identifies a value within a row of the target array. References use dot notation to traverse nested objects:

```
field
parent.child
parent.child.grandchild
```

An identifier begins with a letter or underscore and may contain letters, digits, and underscores. Field names that contain characters outside of this set, or that collide with reserved keywords, must be written using bracket notation with a quoted name:

```
parent['child with spaces']
parent.child['or'].grandchild
```

The quoted name inside brackets follows the same escaping rules as a string literal.

When a path segment resolves to a missing key or to `null`, the entire reference evaluates to `null`. See [Null handling](#null-handling) for how operators treat `null` field values.

### Literals

| Kind | Form |
|---|---|
| String | `'text'` — single-quoted; a single quote is escaped by doubling it (`'don''t'`). |
| Number | Integer, decimal, or scientific notation (e.g. `42`, `-3.14`, `1.5e-2`). A leading minus sign is permitted. |
| Boolean | `true` or `false` (case-insensitive). |
| Null | `null` (case-insensitive). |

Numbers are parsed using the invariant culture; the decimal separator is always `.`.

### Comparison operators

| Operator | Meaning |
|---|---|
| `eq` | Equal to |
| `ne` | Not equal to |
| `gt` | Greater than |
| `ge` | Greater than or equal to |
| `lt` | Less than |
| `le` | Less than or equal to |

Comparison operators are written infix: `field op literal`. The right-hand side must be a literal; comparing two fields is not supported.

Comparison semantics depend on the type of the literal:

- **Numeric literal:** the field value is converted to a number if possible. Numeric types (integer, decimal, floating-point) are converted directly; string fields are parsed using the invariant culture. If the field cannot be interpreted as a number, the comparison falls back to a case-insensitive string comparison of the field's string form against the literal's string form.
- **Boolean literal:** the field value is compared as a boolean. Boolean field values are compared directly; other field values are accepted if their string form parses as `true` or `false`. Otherwise the comparison evaluates to `false` for all operators except `ne`, which evaluates to `true` when the field is non-null but uncomparable.
- **String literal:** if both the field value and the literal parse as date or date-time values, the comparison is performed chronologically in UTC. Otherwise, if the field value is numeric and the literal parses as a number, the comparison is numeric. Otherwise the comparison is a case-insensitive ordinal string comparison.
- **Null literal:** only `eq` and `ne` are meaningful; see [Null handling](#null-handling).

Date and date-time string parsing uses the invariant culture and assumes UTC for values without an explicit offset.

### Set membership

| Operator | Meaning |
|---|---|
| `in (...)` | Field value matches any literal in the list. |
| `nin (...)` | Field value matches none of the literals in the list. |

The list inside the parentheses is a comma-separated sequence of literals. The list may be empty, in which case `in` evaluates to `false` and `nin` evaluates to `true` for every row. Each candidate literal is compared to the field value using the same rules as the `eq` operator. `null` may appear in the list and matches when the field value is `null`.

### String functions

| Function | Meaning |
|---|---|
| `contains(field, 'literal')` | The string form of the field contains the literal. |
| `startswith(field, 'literal')` | The string form of the field begins with the literal. |
| `endswith(field, 'literal')` | The string form of the field ends with the literal. |

All three functions perform case-insensitive ordinal matching. The second argument must be a string literal. If the field value is `null`, the function evaluates to `false`. Non-string field values are converted to their string form before matching.

### Logical operators

| Operator | Precedence |
|---|---|
| `not` | Highest — applies to the immediately following expression. |
| `and` | Middle — left-associative. |
| `or` | Lowest — left-associative. |

Parentheses may be used to override precedence and to group sub-expressions. Logical operator keywords are case-insensitive.

### Null handling

Filter evaluation treats `null` according to the following rules:

- A field reference that traverses a missing or `null` segment resolves to `null`.
- `field eq null` is `true` when the field resolves to `null`.
- `field ne null` is `true` when the field resolves to a non-null value.
- Any other comparison operator (`gt`, `ge`, `lt`, `le`) involving a `null` literal evaluates to `false`.
- When the field value is `null` and the literal is non-null, every comparison evaluates to `false` except `ne`, which evaluates to `true`.
- `in (..., null, ...)` matches a `null` field value; `nin (..., null, ...)` excludes it.
- String functions applied to a `null` field value evaluate to `false`.

## Sorting

The `orderby` parameter accepts a comma-separated list of sort clauses. Each clause is a field reference optionally followed by a direction keyword:

```
field
field asc
field desc
parent.child desc, other.field
```

The direction keyword is case-insensitive. When the direction is omitted the clause sorts ascending. Field references use the same dot and bracket syntax as in filter expressions. At least one clause must be supplied; an empty `orderby` is rejected.

Clauses are applied in the order they appear: the first clause is the primary sort key, the second is the secondary, and so on.

### Sort comparison rules

- Date and date-time strings are recognised and sorted chronologically.
- Values that implement a natural ordering (numbers, booleans, dates, strings) are compared using that ordering. String comparison is ordinal.
- When two values being compared are of different types, they are ordered first by type name and then by their string form, providing a stable but arbitrary total order.
- `null` is treated as the maximum value. In ascending sorts `null` appears last; in descending sorts `null` appears first.

## Pagination

The `page` parameter selects a 1-based page index and the `pageSize` parameter selects the number of rows per page. Both must be positive integers; non-integer or zero/negative values are rejected.

If `page` is omitted it defaults to `1`. If `pageSize` is omitted it defaults to `50`. A requested `pageSize` greater than `500` is silently capped at `500`.

Pagination is applied after filtering and sorting. Requesting a page beyond the end of the filtered result set yields an empty page; the result metadata still reports the total counts as observed before pagination.

## Result metadata

When the query is applied to an array inside an object payload, a `_meta` property is added to that object alongside the (now filtered/sorted/paged) array. The contents depend on which parts of the query were applied:

| Field | When present | Meaning |
|---|---|---|
| `page` | When `page` or `pageSize` was supplied. | The 1-based page index that was returned. |
| `pageSize` | When `page` or `pageSize` was supplied. | The effective page size after defaulting and capping. |
| `total` | When the filter or paging was applied. | The total number of rows after filtering, before pagination. |
| `totalPages` | When `page` or `pageSize` was supplied. | The total number of pages at the effective page size, rounded up. |
| `filteredCount` | When the filter or paging was applied. | The number of rows after filtering. Equal to `total`. |

If the payload is itself an array (rather than an object containing an array), no metadata is attached. Callers that need result counts in that case should request paging, which forces the response shape to include metadata when supported by the endpoint, or rely on the array length.

If neither filtering nor paging is applied — for example, when only `orderby` is supplied — no `_meta` property is added.

## Errors

Malformed query parameters produce a parse error that identifies the offending parameter, the raw input, the column at which the error was detected, and a human-readable message. Conditions that produce a parse error include:

- An unrecognised character or token.
- A string literal that is not terminated before the end of input.
- A numeric literal that is not a valid number.
- A field reference that is missing, malformed, or uses an unquoted name inside brackets.
- A comparison, `in`, or string-function expression with a missing or wrong-typed argument.
- Unbalanced parentheses or brackets.
- An unexpected token at the end of input.
- A `page` or `pageSize` value that is not a positive integer.
- An `orderby` clause with an invalid direction keyword (anything other than `asc` or `desc`).
- An empty `filter` or `orderby` value.

Column positions reported in error messages are 0-based offsets into the raw parameter value.

## Reserved keywords

The following identifiers are reserved by the query grammar and cannot be used as bare field names; reference them via bracket notation if a payload uses them as property names:

```
eq  ne  gt  ge  lt  le
in  nin
and  or  not
true  false  null
contains  startswith  endswith
asc  desc
```

Keyword matching is case-insensitive.
