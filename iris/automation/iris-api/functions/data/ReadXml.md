---
layout: default
title: ReadXml
parent: Iris API functions
has_children: false
has_toc: false
---

# ReadXml

Reads and parses an XML file from base64 encoded data.

## Syntax

```jyro
ReadXml(base64Content)
ReadXml(base64Content, xpath)
```

## Parameters

- **base64Content** (string): Base64 encoded XML file content
- **xpath** (string, optional): XPath expression to select specific elements (default: entire document)

## Returns

- **object** or **array**: The parsed XML as a structured object, or `null` on error. When XPath selects multiple elements, returns an array.

## Description

Parses XML data from a base64-encoded string and converts it to a Jyro object structure. Elements become nested objects, attributes are prefixed with `@`, and text content is stored in `_text`. The element name is stored in `_name`.

Optional XPath expressions can select specific elements from the document.

## Examples

```jyro
# Read entire XML document
var data = ReadXml(Data.ExternalFile)
if data != null then
    Log("Information", "Root element: " + data._name)
end
```

```jyro
# Use XPath to select specific elements
var items = ReadXml(Data.ExternalFile, "//item")
if items != null then
    foreach item in items do
        Log("Information", "Item: " + item.name._text)
    end
end
```

```jyro
# Process XML with nested structure
# Given XML:
# <catalog>
#   <book id="1">
#     <title>Programming Guide</title>
#     <author>John Doe</author>
#     <price>29.99</price>
#   </book>
# </catalog>

var catalog = ReadXml(Data.ExternalFile)

foreach book in catalog.book do
    Log("Information", "Book ID: " + book["@id"])
    Log("Information", "Title: " + book.title._text)
    Log("Information", "Author: " + book.author._text)
    Log("Information", "Price: " + book.price._text)
end
```

```jyro
# XPath to select books by attribute
var expensiveBooks = ReadXml(Data.ExternalFile, "//book[@price > 50]")
if expensiveBooks != null then
    foreach book in expensiveBooks do
        Log("Information", "Expensive book: " + book.title._text)
    end
end
```

```jyro
# Import organizations from XML
# Given XML:
# <organizations>
#   <organization active="true">
#     <name>Acme Corp</name>
#     <description>Manufacturing company</description>
#     <facets>
#       <facet name="Industry">Manufacturing</facet>
#     </facets>
#   </organization>
# </organizations>

var data = ReadXml(Data.ExternalFile)

if data == null then
    Log("Error", "Failed to parse XML")
    return
end

foreach org in data.organization do
    var isActive = org["@active"] == "true"

    if isActive then
        var orgId = CreateOrganization(
            org.name._text,
            org.description._text,
            "Replace"
        )

        if orgId != null and org.facets != null then
            foreach facet in org.facets.facet do
                var facetDef = GetFacetDefinitionByName(facet["@name"])
                if facetDef != null then
                    SetOrganizationFacetInstance(orgId, facetDef.id, [facet._text])
                end
            end
        end
    end
end
```

```jyro
# Handle single vs multiple elements
var items = ReadXml(Data.ExternalFile, "//item")

# XPath may return single object or array
if items._name != null then
    # Single element returned as object
    Log("Information", "Single item: " + items.name._text)
else
    # Multiple elements returned as array
    foreach item in items do
        Log("Information", "Item: " + item.name._text)
    end
end
```

## XML to Object Mapping

| XML Feature | Jyro Property | Example |
|-------------|---------------|---------|
| Element name | `_name` | `"book"` |
| Attribute | `@attributeName` | `obj["@id"]` → `"123"` |
| Text content | `_text` | `obj._text` → `"Hello"` |
| Child element | Nested object | `obj.child` |
| Multiple same-name children | Array | `obj.items` → `[...]` |

## XPath Support

Common XPath expressions:
- `//item` - Select all `<item>` elements anywhere
- `/root/items/item` - Select `<item>` elements at specific path
- `//item[@type='active']` - Select elements with attribute value
- `//item[position() <= 5]` - Select first 5 items

## Error Handling

The function returns `null` on error, including:
- Invalid base64 encoding
- Malformed XML syntax
- Invalid XPath expression

Returns empty array when XPath matches no elements.

## Related Functions

- [ReadCsv](ReadCsv) - Read CSV files
- [ReadJson](ReadJson) - Read JSON files
- [ReadExcel](ReadExcel) - Read Excel (.xlsx) files
