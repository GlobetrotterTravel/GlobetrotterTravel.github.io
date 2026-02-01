---
layout: default
title: Automation
parent: Globetrotter Iris
nav_order: 80
has_children: true
has_toc: false
permalink: /iris/automation/
---

# Automation
{: .no_toc }

Iris comes with a full automation and workflow engine out of the box. The Iris automation capabilities fall into one of three areas:

* Event hooks
* Dynamic endpoints
* Scheduled scripts

Additionally, Iris has a rich API of data manipulation functions that can be called within automation scripts. Anything that a human user can do in the Iris interface, a script can do by calling one or more Iris API functions.

## In this section

### Core concepts

- [Jyro language](/iris/automation/jyro-language/): The scripting language used for all automation in Iris
- [Data context](/iris/automation/data-context/): Information available to your scripts
- [Event hooks](/iris/automation/event-hooks/): Execute scripts when domain events occur
- [Dynamic endpoints](/iris/automation/dynamic-endpoints/): Create custom API endpoints
- [Script scheduling](/iris/automation/script-scheduling/): Run scripts on a schedule

### Supporting features

- [Templates](/iris/automation/templates/): Render dynamic content using Scriban templates
- [Log lines](/iris/automation/log-lines/): Script logging for debugging
- [Notepads](/iris/automation/notepads/): Persistent script output storage
- [Event queue](/iris/automation/event-queue/): Async processing and execution history

### Reference

- [Iris API](/iris/automation/iris-api/): Complete API reference for Jyro scripts