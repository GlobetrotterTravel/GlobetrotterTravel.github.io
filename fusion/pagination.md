---
layout: default
title: Pagination
parent: Globetrotter Fusion
nav_order: 50
permalink: /fusion/pagination/
---

# Pagination
{: .no_toc}

Fusion supports pagination to manage the retrieval of large datasets efficiently. By default, Fusion returns a limited number of records per request. The `fetch` and `offset` parameters allow you to control the number of records retrieved and navigate through the dataset.

## Table of contents
{: .no_toc}

1. TOC
{:toc}

## Default Behavior

- **Default `fetch` Value**: If not specified, Fusion returns **10** records per request.
- **Default `offset` Value**: If not specified, Fusion starts from the **first record** (offset `0`).

## `fetch` Parameter

Specifies the maximum number of records to return in a single response. To adjust the number of records returned, include the `fetch` parameter in your request URL. If `fetch` is less than `1` or not a valid number, it defaults to **10**, while if `fetch` exceeds `100`, it is set to **100**.

### Examples

1. **Retrieve 5 Records**:

   ```url
   GET https://fusion.globetrotter.com.au/api/v1/invoices?fetch=5
   ```

   Returns a maximum of 5 records.

2. **Attempt to Retrieve More Than 100 Records**:

   ```url
   GET https://fusion.globetrotter.com.au/api/v1/invoices?fetch=150
   ```

   Since `fetch` exceeds the maximum allowed value, it will be capped at **100**.

## `offset` Parameter

Specifies the number of records to skip before starting to return records. Use the `offset` parameter to retrieve subsequent pages of results.

### Examples

1. **Skip the First 20 Records**:

   ```url
   GET https://fusion.globetrotter.com.au/api/v1/bookings?offset=20
   ```

   Starts returning records from the 21st record onward.

2. **Retrieve Records in Batches of 50**:

   ```url
   GET https://fusion.globetrotter.com.au/api/v1/itineraries?offset=0&fetch=50
   ```

   Retrieves records 1 to 50.

   ```url
   GET https://fusion.globetrotter.com.au/api/v1/itineraries?offset=50&fetch=50
   ```

   Retrieves records 51 to 100.

## Pagination Strategy

To retrieve all records in a dataset larger than 100 records, increment the `offset` by the `fetch` value in each subsequent request.

**Example**:

Retrieve all invoices in batches of 100 records:

1. **First Request** (records 1–100):

   ```url
   GET https://fusion.globetrotter.com.au/api/v1/invoices?offset=0&fetch=100
   ```

2. **Second Request** (records 101–200):

   ```url
   GET https://fusion.globetrotter.com.au/api/v1/invoices?offset=100&fetch=100
   ```

3. **Third Request** (records 201–300):

   ```url
   GET https://fusion.globetrotter.com.au/api/v1/invoices?offset=200&fetch=100
   ```

4. **Continue Incrementing `offset`**:

   Increase the `offset` by `100` each time until Fusion returns an empty `result` array.

## Handling Empty Results

When there are no more records to retrieve, Fusion returns an empty array in the `result` field:

```json
{
    "result": []
}
```

This indicates that all records have been retrieved, and you can stop making further requests.

> **🛈 An empty `result` array means there are no records matching the current `query` and `offset`.**
> **🛈 When using `offset` with a `query`, the `offset` applies to the filtered results.**
> **🛈 Refer to the endpoint documentation to find out the ordering key.**

## Combining `fetch`, `offset`, and `query`

You can combine pagination parameters with queries to retrieve filtered data in manageable chunks.

**Example**:

Retrieve all bookings for `ClientNumber` 12345 in batches of 50 records:

1. **First Request** (records 1–50):

   ```url
   GET https://fusion.globetrotter.com.au/api/v1/bookings?query=ClientNumber^EQ12345&offset=0&fetch=50
   ```

2. **Second Request** (records 51–100):

   ```url
   GET https://fusion.globetrotter.com.au/api/v1/bookings?query=ClientNumber^EQ12345&offset=50&fetch=50
   ```

3. **Continue Incrementing `offset`**:

   Increase the `offset` by `50` each time until you receive an empty `result` array.

**Important**:

> **🛈 The `offset` applies **after** the query filters the records.**
> **🛈 Ensure your `offset` and `fetch` values are consistent across requests for accurate pagination.**

## Best Practices

- **Monitor for Empty Results**:
  - Use the empty `result` array to determine when to stop paginating.
  
- **Consistent `fetch` Size**:
  - Keep the `fetch` size consistent to simplify pagination logic.
  
- **Avoid Large `offset` Values**:
  - Large `offset` values may lead to performance issues.
  - Consider refining your query to reduce the dataset size.

## Limitations

> **🛈 There is no explicit maximum `offset`, but extremely high values might result in performance degradation or timeouts.**

**Rate Limiting**:
> **⚠ Fusion may rate-limit requests that exceed safe thresholds. Systems should be designed to back-off and retry if rate limiting occurs.**

## Example Scenario

**Goal**: Retrieve all itineraries starting from September 1, 2023, for `ClientNumber` 12345.

**Steps**:

1. **Define the Query**:

   ```url
   query=ClientNumber^EQ12345;BookingDate^GE2023-09-01
   ```

2. **Initialize Pagination Parameters**:

   - `offset`: `0`
   - `fetch`: `100`

3. **First Request**:

   ```url
   GET https://fusion.globetrotter.com.au/api/v1/itineraries?query=ClientNumber^EQ12345;BookingDate^GE2023-09-01&offset=0&fetch=100
   ```

4. **Subsequent Requests**:

   - Increment `offset` by `100` with each request.
   - Continue until an empty `result` array is received.

**Code Snippet** (pseudo-code):

```pseudo
offset = 0
fetch = 100
do {
    response = GET /itineraries?query=...&offset=offset&fetch=fetch
    process(response.result)
    offset += fetch
} while (response.result is not empty)
```

## Troubleshooting

- **Empty `result` Before Expected**:
  - Verify that your query parameters are correct.
  - Ensure that the `offset` has not exceeded the number of available records.

- **Duplicate or Missing Records**:
  - Confirm that the default ordering has not changed between requests.
  - Ensure that `offset` increments match the `fetch` size.

- **Performance Issues**:
  - Reduce the `fetch` size if experiencing timeouts.
  - Optimize your query to narrow down the results.
