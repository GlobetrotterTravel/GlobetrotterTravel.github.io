---
layout: default
parent: Fusion
title: The query engine
title_display: true
nav_order: 40
permalink: /fusion/the-query-engine/
---

# The query engine
{: .no_toc}

The query engine interprets a query string provided to an endpoint and returns documents matching that search query. All endpoints support query engine searches.

## Table of contents
{: .no_toc}

1. TOC
{:toc}

## Overview

Queries are appended to API endpoints using the `?query` parameter in the URL. The query string consists of one or more clauses that define the filtering criteria. Each clause specifies a property, an operator, and a value.

## Query format

The general format of a query clause is:

```
Property^OperatorValue
```

Multiple clauses can be combined by separating them with semicolons (`;`):

```
Property1^Operator1Value1;Property2^Operator2Value2;...
```

- **Property**: The name of the data field to filter (case-insensitive).
- **Operator**: The operation to perform for filtering.
- **Value**: The value to compare against the property.

## Supported operators

The following operators are supported for constructing queries:

| Query Operator | Description                            | Applies to Types                |
|----------------|----------------------------------------|---------------------------------|
| `^EQ`          | Equal to                               | String, Numeric, Date, Boolean* |
| `^NE`          | Not equal to                           | String, Numeric, Date, Boolean* |
| `^GT`          | Greater than                           | Numeric, Date                   |
| `^LT`          | Less than                              | Numeric, Date                   |
| `^GE`          | Greater than or equal to               | Numeric, Date                   |
| `^LE`          | Less than or equal to                  | Numeric, Date                   |
| `^IN`          | In comma-separated list (inclusive)    | String, Numeric, Date           |
| `^NI`          | Not in comma-separated list (exclusive)| String, Numeric, Date           |
| `^CT`          | Contains string                        | String                          |

\* Boolean arguments can be specified as either `true`/`false` or `1`/`0`.

## Limitations

The query engine has the following general limitations:

- **Semicolons in Values**: Semicolons are interpreted as clause separators. If a value contains a semicolon, it must be URL-encoded.
- **No OR Queries**: Queries are additive and combined using logical AND. OR queries are not supported.
- **Nested Properties**: Searching on properties within nested objects or arrays (e.g., `Travellers`, `LineItems`, `Segments`) is not supported. Only top-level properties of the DTOs can be queried.

## Specifying the query parameter

Ensure that the `?query` parameter is specified. Unknown arguments are silently ignored, e.g.

<pre><code>GET https://fusion.globetrotter.com.au/api/v1/itineraries<span style="color:red; font-weight:bold;">?query=</span>TripStartLocal^GT2023-09-01</code></pre>

is correct, whereas

```
GET https://fusion.globetrotter.com.au/api/v1/itineraries?TripStartLocal^GT2023-09-01
```

lacks the `?query` parameter and will therefore just return all itineraries.

## Value specifications

- **Strings**: Should be provided as-is. For special characters or spaces, URL encoding may be required.

  ```
  GET https://fusion.globetrotter.com.au/api/v1/bookings?query=ClientNumber^EQ12345
  ```

- **Numbers**: Should be provided without quotes or formatting.

  ```
  GET https://fusion.globetrotter.com.au/api/v1/invoices?query=TotalAmount^GT1000
  ```

- **Dates**: Should be provided in the format `YYYY-MM-DD`. Only the date component is considered; time is ignored.

  ```
  GET https://fusion.globetrotter.com.au/api/v1/itineraries?query=BookingDate^GE2023-01-01
  ```

- **Null Values**: To filter for `null` values, use the keyword `null` (case-insensitive).

  ```
  GET https://fusion.globetrotter.com.au/api/v1/bookings?query=AuthorisedBy^EQnull
  ```

- **Booleans**: Specify as `true`, `false`, `1`, or `0`.

  ```
  GET https://fusion.globetrotter.com.au/api/v1/bookings?query=IsInternational^EQtrue
  ```

## Operator descriptions with examples

### Equal To (`^EQ`)

Matches records where the property is equal to the specified value.

**Example**: Retrieve all bookings with a specific `ClientNumber`.

```
GET https://fusion.globetrotter.com.au/api/v1/bookings?query=ClientNumber^EQ12345
```

### Not Equal To (`^NE`)

Matches records where the property is not equal to the specified value.

**Example**: Retrieve all invoices not associated with `ClientNumber` 12345.

```
GET https://fusion.globetrotter.com.au/api/v1/invoices?query=ClientNumber^NE12345
```

### Greater Than (`^GT`)

Matches records where the property is greater than the specified value (numeric or date).

**Example**: Retrieve all invoices with a `TotalAmount` greater than 1,000.

```
GET https://fusion.globetrotter.com.au/api/v1/invoices?query=TotalAmount^GT1000
```

### Less Than (`^LT`)

Matches records where the property is less than the specified value (numeric or date).

**Example**: Retrieve all itineraries booked before January 1, 2023.

```
GET https://fusion.globetrotter.com.au/api/v1/itineraries?query=BookingDate^LT2023-01-01
```

### Greater Than or Equal To (`^GE`)

Matches records where the property is greater than or equal to the specified value.

**Example**: Retrieve all bookings modified on or after July 1, 2023.

```
GET https://fusion.globetrotter.com.au/api/v1/bookings?query=ModifiedDateTime^GE2023-07-01
```

### Less Than or Equal To (`^LE`)

Matches records where the property is less than or equal to the specified value.

**Example**: Retrieve all invoices with a `BalanceAmount` less than or equal to zero.

```
GET https://fusion.globetrotter.com.au/api/v1/invoices?query=BalanceAmount^LE0
```

### In List (`^IN`)

Matches records where the property is equal to any value in a comma-separated list (case-insensitive).

**Example**: Retrieve all bookings made by specific agencies.

```
GET https://fusion.globetrotter.com.au/api/v1/bookings?query=OriginatingAgency^INAgencyA,AgencyB
```

### Not In List (`^NI`)

Matches records where the property is not equal to any value in a comma-separated list (case-insensitive).

**Example**: Retrieve all itineraries not associated with certain departments.

```
GET https://fusion.globetrotter.com.au/api/v1/itineraries?query=Department^NIHR,Finance
```

### Contains (`^CT`)

Matches records where the property contains the specified substring (case-insensitive).

**Example**: Retrieve all bookings where `Traveller` contains "Smith".

```
GET https://fusion.globetrotter.com.au/api/v1/bookings?query=Traveller^CTSmith
```

## Combining multiple clauses

Multiple clauses can be combined to create complex queries. Clauses are evaluated using an implicit logical AND.

**Example**: Retrieve all international bookings that are ticketed.

```
GET https://fusion.globetrotter.com.au/api/v1/bookings?query=IsInternational^EQtrue;IsTicketed^EQtrue
```

**Example**: Retrieve all invoices for `ClientNumber` 12345 with a `TotalAmount` greater than 1,000.

```
GET https://fusion.globetrotter.com.au/api/v1/invoices?query=ClientNumber^EQ12345;TotalAmount^GT1000
```

## Case sensitivity

- **Property Names**: Case-insensitive.
- **String Values**: Compared in a case-insensitive manner for all operators.
- **`^CT` Operator**: Performs a case-insensitive substring search.

## Handling special characters

When values contain special characters (e.g., spaces, commas, semicolons), URL encoding must be used.

- Space: `%20`
- Comma: `%2C`
- Semicolon: `%3B`
- Caret (`^`): `%5E`

**Example**: Retrieve bookings with `AuthorisedBy` equal to "John Doe".

```
GET https://fusion.globetrotter.com.au/api/v1/bookings?query=AuthorisedBy^EQJohn%20Doe
```

## Error handling

Invalid queries will result in error messages indicating the issue. Common errors include:

- **Missing Operator**: Occurs when the operator is not provided.

  ```
  Error: "Invalid query format: Missing operator."
  ```

- **Unknown Operator**: Occurs when an unsupported operator is used.

  ```
  Error: "Invalid query format: Unknown operator 'XYZ'."
  ```

- **Null or Empty Value**: Occurs when a value is not provided after the operator.

  ```
  Error: "Invalid query format: Argument for property 'ClientNumber' is null or empty."
  ```

- **Invalid Numeric or Date Value**: Occurs when a non-numeric value is used with a numeric operator.

  ```
  Error: "Invalid query format: Expected numeric or date value for operator '^GT' on property 'TotalAmount', but got 'abc'."
  ```

- **Unknown Property**: Occurs when the specified property does not exist in the data model.

  ```
  Error: "Unknown search property: 'InvalidProperty' not found on type BookingGetDtoV1."
  ```

## Examples

### Example 1: filtering bookings by client number

Retrieve all bookings for `ClientNumber` 12345.

```
GET https://fusion.globetrotter.com.au/api/v1/bookings?query=ClientNumber^EQ12345
```

### Example 2: filtering invoices by date range

Retrieve all invoices dated between January 1, 2023, and June 30, 2023.

```
GET https://fusion.globetrotter.com.au/api/v1/invoices?query=InvoiceDate^GE2023-01-01;InvoiceDate^LE2023-06-30
```

### Example 3: filtering itineraries by departments

Retrieve all itineraries associated with the `Sales` or `Marketing` departments.

```
GET https://fusion.globetrotter.com.au/api/v1/itineraries?query=Department^INSales,Marketing
```

### Example 4: filtering bookings by international flag

Retrieve all domestic (non-international) bookings.

```
GET https://fusion.globetrotter.com.au/api/v1/bookings?query=IsInternational^EQfalse
```

### Example 5: filtering invoices by outstanding balance

Retrieve all invoices with an outstanding `BalanceAmount` greater than zero.

```
GET https://fusion.globetrotter.com.au/api/v1/invoices?query=BalanceAmount^GT0
```

### Example 6: filtering itineraries by travel dates

Retrieve all itineraries with a `TripStartLocal` date after September 1, 2023.

```
GET https://fusion.globetrotter.com.au/api/v1/itineraries?query=TripStartLocal^GT2023-09-01
```

### Example 7: combining multiple conditions

Retrieve all ticketed bookings for `ClientNumber` 12345 where `Traveller` contains "Doe".

```
GET https://fusion.globetrotter.com.au/api/v1/bookings?query=ClientNumber^EQ12345;IsTicketed^EQtrue;Traveller^CTDoe
```

## Special notes

- **Ordering of clauses**: The order of clauses does not affect the outcome.

  ```
  GET https://fusion.globetrotter.com.au/api/v1/bookings?query=IsTicketed^EQtrue;ClientNumber^EQ12345
  ```

  is equivalent to:

  ```
  GET https://fusion.globetrotter.com.au/api/v1/bookings?query=ClientNumber^EQ12345;IsTicketed^EQtrue
  ```

- **Date and time handling**: For properties of type `DateTime`, only the date component is considered during comparisons.

- **Whitespace handling**: Leading and trailing whitespaces in values are trimmed.

- **Nested properties**: Cannot query nested properties such as `Travellers.Name` or `LineItems.Product`.

## Tips for effective querying

- **Ensure that the `?query` parameter is specified**: Unknown arguments are silently ignored, e.g.

<pre><code>GET https://fusion.globetrotter.com.au/api/v1/itineraries<span style="color:red; font-weight:bold;">?query=</span>TripStartLocal^GT2023-09-01</code></pre>

is correct, whereas

```
GET https://fusion.globetrotter.com.au/api/v1/itineraries?TripStartLocal^GT2023-09-01
```

lacks the `?query` parameter and will therefore just return all itineraries.

- **Use correct property names**: Refer to the DTO definitions to ensure property names are accurate.

- **Format dates correctly**: Use the `YYYY-MM-DD` format for dates.

- **Specify booleans correctly**: Use `true`/`false` or `1`/`0` for boolean values.

- **URL-encode special characters**: Encode spaces, commas, and other special characters in query values.

- **Test queries**: Validate queries in a test environment before deploying.

## HTTP response codes

Fusion will return the following HTTP response codes:

### 200 [OK]

Indicates a successful request where records were retrieved or no records matched the query.

```json
{
    "result": []
}
```

### 400 [Bad Request]

Occurs when the query is malformed, such as using invalid property names or operators.

### 401 [Unauthorized]

Occurs when authentication fails due to missing or incorrect credentials.

### 404 [Not Found]

Occurs when an invalid endpoint or API version is specified.

### 503 [Service Unavailable]

Occurs when Fusion's cache is not ready. Retry after a few minutes.

### 500 [Internal Server Error]

Indicates an unexpected error occurred on the server.

## Troubleshooting

- **Verify property names**: Ensure they match those in the DTOs.

- **Check operators**: Use supported operators and apply them to the correct data types.

- **Validate values**: Ensure values are in the correct format for the property data type.

- **Encode special characters**: Use URL encoding for values with special characters.