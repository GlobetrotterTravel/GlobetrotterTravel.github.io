# Dynamic Endpoints

Dynamic endpoints allow administrators to create a set of custom response payloads in almost any format required. By creating a payload template and binding with a data model passed in by Iris, the endpoint can respond to requestors in formats ranging from JSON and XML to CSV or even plain text. This allows administrators to solve problems that the built in APIs do not address.

Iris' built-in endpoints are specified in the `/api` namespace e.g.

```url
/tenant/0e8ea786-96c2-41f6-9be3-658dbd5df318/api/v1/...
```

Iris's dynamic endpoints are in the `dapi` namespace, i.e.

```url
/tenant/0e8ea786-96c2-41f6-9be3-658dbd5df318/dapi/...
```
For example

```url
/tenant/0e8ea786-96c2-41f6-9be3-658dbd5df318/dapi/some/path/to/my-customised-endpoint
```

Almost any endpoint string can be specified including subpaths. Versioning can be acheived by adding a `v` suffix, e.g.

```url
/tenant/0e8ea786-96c2-41f6-9be3-658dbd5df318/dapi/v1/...
```

Dynamic endpoints are read-only. Writing to dynamic endpoints is not supported.

## Creating a dynamic endpoint

To create a dynamic endpoint supply the following information

* **Name**: The name of the endpoint
* **String**: The endpoint's path string
* **Template**: The Razor (cshtml) file that contains the layout for the return payload
* **Component definition**: The component definition to return objects for
* **Content-type**: The content type to set the response object to

## Razor templates

Each dynamic endpoint has a nominated Razor template. Razor is a powerful templating engine used most famously in ASP.NET to render webpages. By structuring the payload template in an external cshtml file, administrators have fine-grained control over the output of the dynamic endpoint. Although a full treatment of Razor syntax is outside the scope of this document, more information can be found [https://learn.microsoft.com/en-us/aspnet/core/mvc/views/razor?view=aspnetcore-9.0](here).

Only a subset of Razor directives are allowed. Specifically, code blocks (@{}) are not allowed. The following directives are permitted:

* @if statements
* @foreach loops
* @switch statements
* @Model (the data passed in by Iris)
* Razor comments

Templates containing directives that are not on the allow list will not be loaded.