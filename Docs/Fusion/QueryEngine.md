# The Query Engine

The query engine interprets a query string provided to an endpoint and returns documents matching that search query. All endpoints support query engine searches.

## Overview

Queries are appended to API endpoints using the `query` parameter in the URL. The query string consists of one or more clauses that define the filtering criteria. Each clause specifies a property, an operator, and a value.

## Query Format

The general format of a query clause is:

```
Property^OperatorValue
```

Multiple clauses can be combined by separating them with semicolons (`;`):

```
Property1^Operator1Value1;Property2^Operator2Value2;...
```

- **Property**: The name of the data field to filter.
- **Operator**: The operation to perform for filtering.
- **Value**: The value to compare against the property.

## Supported Operators

The following operators are supported for constructing queries:

- `^EQ`: Equal to
- `^NE`: Not equal to
- `^GT`: Greater than
- `^LT`: Less than
- `^GE`: Greater than or equal to
- `^LE`: Less than or equal to
- `^IN`: In a list of values
- `^NI`: Not in a list of values
- `^CT`: Contains (substring match)

### Operator Descriptions

- **Equal To (`^EQ`)**: Matches records where the property is equal to the specified value.

  ```
  ?query=recordLocator^EQABC123
  ```

- **Not Equal To (`^NE`)**: Matches records where the property is not equal to the specified value.

  ```
  ?query=costCentre^NEExCo
  ```

- **Greater Than (`^GT`)**: Matches records where the property is greater than the specified value. Applicable to numeric and date properties.

  ```
  ?query=totalAirDistance^GT1000
  ```

- **Less Than (`^LT`)**: Matches records where the property is less than the specified value. Applicable to numeric and date properties.

  ```
  ?query=totalAirDistance^LT1000
  ```

- **Greater Than or Equal To (`^GE`)**: Matches records where the property is greater than or equal to the specified value. Applicable to numeric and date properties.

  ```
  ?query=Score^GE85
  ```

- **Less Than or Equal To (`^LE`)**: Matches records where the property is less than or equal to the specified value. Applicable to numeric and date properties.

  ```
  ?query=Date^LE2023-12-31
  ```

- **In List (`^IN`)**: Matches records where the property is equal to any value in a comma-separated list. Comparison is case-insensitive.

  ```
  ?query=department^INExCo,Operations
  ```

- **Not In List (`^NI`)**: Matches records where the property is not equal to any value in a comma-separated list. Comparison is case-insensitive.

  ```
  ?query=department^NIExCo,Operations
  ```

- **Contains (`^CT`)**: Matches records where the property contains the specified substring. Comparison is case-insensitive.

  ```
  ?query=CustomData1^CTEXT12
  ```

## Value Specifications

- **Strings**: Should be provided as-is. For special characters or spaces, URL encoding may be required.

  ```
  ?query=Name^EQJohn%20Doe
  ```

- **Numbers**: Should be provided without quotes or formatting.

  ```
  ?query=Age^GT30
  ```

- **Dates**: Should be provided in a standard date format (e.g., `YYYY-MM-DD`). Only the date component is considered; time is ignored.

  ```
  ?query=StartDate^GE2023-01-01
  ```

- **Null Values**: To filter for `null` values, use the keyword `null` (case-insensitive).

  ```
  ?query=EndDate^EQnull
  ```

## Combining Multiple Clauses

Multiple clauses can be combined to create complex queries. Clauses are evaluated using an implicit logical AND, meaning that all conditions must be met for a record to be included in the results.

```
?query=Status^EQActive;Age^GT25;Department^INHR,Finance
```

This query filters records where:

- `Status` is equal to `Active`.
- `Age` is greater than `25`.
- `Department` is either `HR` or `Finance`.

## Case Sensitivity

- Property names are case-insensitive.
- String values are compared in a case-insensitive manner for all operators.
- The `^CT` (Contains) operator performs a case-insensitive substring search.

## Handling Special Characters

When values contain special characters (e.g., spaces, commas, semicolons), URL encoding must be used to ensure proper parsing of the query string.

- Space: `%20`
- Comma: `%2C`
- Semicolon: `%3B`
- Caret (`^`): `%5E`

Example:

```
?query=Department^EQMinistry%20Of%20Silly%20Walks
```

## Data Type Considerations

- **Numeric Comparisons**: Operators such as `^GT`, `^LT`, `^GE`, and `^LE` require numeric or date values. If a non-numeric value is provided, an error will occur.

  ```
  ?query=Price^GT199.99
  ```

- **Date Comparisons**: Date strings should be in a recognizable format (e.g., `YYYY-MM-DD`). Only the date component is compared, not the time.

  ```
  ?query=OrderDate^LE2023-06-30
  ```

- **String Comparisons**: For operators like `^EQ`, `^NE`, `^IN`, `^NI`, and `^CT`, values are treated as strings.

  ```
  ?query=Status^EQPending
  ```

## Error Handling

Invalid queries will result in error messages indicating the issue. Common errors include:

- **Missing Operator**: Occurs when a caret (`^`) operator is not provided between the property and value.

  ```
  Error: "Invalid query format: Missing operator."
  ```

- **Unknown Operator**: Occurs when an unsupported operator is used.

  ```
  Error: "Invalid query format: Unknown operator 'XYZ'."
  ```

- **Null or Empty Value**: Occurs when a value is not provided after the operator.

  ```
  Error: "Invalid query format: Argument for property 'Age' is null or empty."
  ```

- **Invalid Numeric or Date Value**: Occurs when a non-numeric value is used with a numeric operator.

  ```
  Error: "Invalid query format: Expected numeric or date value for operator '^GT' on property 'Age', but got 'abc'."
  ```

- **Unknown Property**: Occurs when the specified property does not exist in the data model.

  ```
  Error: "Unknown search property: 'InvalidProperty' not found on type Employee."
  ```

## Examples

### Example 1: Filtering by Exact Match

Retrieve all records where the `Status` is `Active`.

```
?query=Status^EQActive
```

### Example 2: Filtering by Range

Retrieve all records where the `Age` is between `18` and `30`.

```
?query=Age^GE18;Age^LE30
```

### Example 3: Filtering by Inclusion in a List

Retrieve all records where the `Category` is either `Books`, `Electronics`, or `Clothing`.

```
?query=Category^INBooks,Electronics,Clothing
```

### Example 4: Filtering by Exclusion from a List

Retrieve all records where the `Region` is not `North America` or `Europe`.

```
?query=Region^NINorth%20America,Europe
```

### Example 5: Filtering by Substring Match

Retrieve all records where the `Title` contains the word `Manager`.

```
?query=Title^CTManager
```

### Example 6: Filtering by Null Values

Retrieve all records where the `Supervisor` field is `null`.

```
?query=Supervisor^EQnull
```

### Example 7: Combining Multiple Conditions

Retrieve all active employees in the `Engineering` department over the age of `30`.

```
?query=Status^EQActive;Department^EQEngineering;Age^GT30
```

## Special Notes

- **Ordering of Clauses**: The order in which clauses are specified does not affect the outcome.

  ```
  ?query=Age^GT30;Status^EQActive
  ```

  is equivalent to:

  ```
  ?query=Status^EQActive;Age^GT30
  ```

- **Whitespace Handling**: Leading and trailing whitespaces in values are trimmed during processing.

- **Value Parsing**: The engine attempts to parse values into appropriate data types (e.g., numeric, date) based on the operator used.

- **Time Components in Dates**: When comparing dates, the time component is disregarded. For example, `2023-07-15T14:30:00` is treated as `2023-07-15`.

## Tips for Effective Querying

- **Ensure Correct Data Types**: When using operators that require numeric or date values, verify that the provided values are in the correct format.

- **Use URL Encoding**: Always encode special characters to prevent parsing errors.

- **Test Queries**: Before deploying queries in production environments, test them to ensure they return the expected results.

- **Handle Nulls Appropriately**: Use the `null` keyword to filter for properties that are null or not null.

- **Combine Clauses for Specificity**: Use multiple clauses to narrow down results and retrieve only the most relevant data.

## Troubleshooting

If unexpected results are returned or errors occur:

- **Check Property Names**: Verify that property names are spelled correctly and exist in the data model.

- **Validate Operators**: Ensure that the correct operator is used for the intended comparison.

- **Inspect Values**: Confirm that values are correctly formatted and appropriate for the operator.

- **Review URL Encoding**: Make sure all special characters are properly URL-encoded.
