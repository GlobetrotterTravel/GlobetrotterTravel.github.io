---
layout: default
title: Homepage
parent: Globetrotter Iris
nav_order: 10
has_children: true
has_toc: false
permalink: /iris/homepage/
---

# Home
{: .no_toc }

The Iris homepage is a dynamic report that appears when you first log in to Iris.

It can be configured by the systems administrator to display an almost unlimited array of data from both Iris itself and external sources.

The homepage is driven by a special dynamic endpoint called `/api/dynamic/home`. When Iris displays the homepage, this dynamic endpoint is called and the contents are returned.

The dynamic endpoint should:

* Be placed at `/api/dynamic/home` (where the homepage expects to find it)
* Return a `_payload` by calling `RenderTemplate()` with a Scriban template to lay out the report in a custom format
* Return a `_contentType` of `text/html`
* Return a `_statusCode` of `200`

Apart from these requirements, the data and template can be of almost any configuration or layout.

## In this section

- [Setting the homepage](/iris/home/setting-the-homepage/): How to configure a static or dynamic homepage
