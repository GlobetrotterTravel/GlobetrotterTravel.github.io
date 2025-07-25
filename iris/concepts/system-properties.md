---
layout: default
title: System Properties
parent: Concepts
has_children: false
has_toc: false
permalink: /iris/concepts/system-properties/
---

# System properties

System properties allow tenant administrators to customize certain aspects of their Iris tenancies. A list of system properties with default values is defined by the Iris system. These default values are used when system property value has not been set for a tenant. When a system property value is set for a tenant, the Iris system uses that value instead of the default. If the value is invalid, Iris falls back to using the default value.

The list of system properties cannot be modified, although new system properties may be made available by Globetrotter from time to time.

## Values

System properties accept only a single data type: string. Where a setting is a boolean value, the following strings are accepted:

`True`/`False`
`Yes`/`No`
`On`/`Off`
`0`/`1`

Values in system properties are case-insensitive.

## System property naming

System properties are named using a dot-separated namespace layout, where strings at the start of the name specify broad categories and the name gets more specific the further to the right you go.

Normally, the system properties are named in this style:

`subsystem.category.identifier`

This makes it easy to locate a particular system property when sorted alphabetically.

## System property descriptions

Each system property has a description that specifies what it does, and some possible use cases. Where appropriate, the description will provide example values.