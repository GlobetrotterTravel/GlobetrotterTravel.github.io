# Pagination

By default, Fusion returns 10 records at a time. This maximum can be lowered by specifying the `fetch` argument with a value, for example

````url
GET https://fusion.globetrotter.com.au/api/v1/invoices?fetch=5
````

Will return a maximum of 5 records (and less if less than 5 records exist).

If `fetch` is supplied a value higher than 100, then the value is ignored and set to 10.

To return more than 100 records, use the `offset` argument. The `offset` argument specifies the number of rows to skip before returning. Calling Fusion iteratively by `offset`ting the cumulative `fetch`ed number of rows until the return document is blank will return all available rows in 100-record chunks.

for example

````url
GET https://fusion.globetrotter.com.au/api/v1/invoices?offset=400
````

will skip the first 400 rows (ordered by `invoiceDate` as specified on the invoices endpoint). To return all invoices, repeatedly call the endpoint with no query while supplting `offset` and `fetch` values, i.e.

````url
GET https://fusion.globetrotter.com.au/api/v1/invoices?offset=0&fetch=100
````

Will fetch the first 100 records starting at the oldest invoice (note that these values may be ommitted as they are defaults).

Calling

````url
GET https://fusion.globetrotter.com.au/api/v1/invoices?offset=100&fetch=100
````

will fetch records 101-200,

````url
GET https://fusion.globetrotter.com.au/api/v1/invoices?offset=200&fetch=100
````

will fetch the next 100 after that (records 201-300).

Routines can be written to loop through the endpoint calls until `result` is null, i.e.

````json
{
    "result": []
}
````

> **🛈 When using `offset` with `query`, remember that the query will condense the number of records first. If the `offset` value exceeds the number of records returned by a query, then a null `result` is returned.**
