---
layout: default
title: Lost Opportunity
parent: Focus Areas
nav_order: 10
has_children: false
permalink: /insight/focus-areas/lost-opportunity/
---

# Lost opportunity
{: .no_toc}

{: .warning } This is pre-release information and is subject to change. No action should be taken on the information in this article. No warranty as to the reliability or accuracy of the information presented in this article is offered.

Highlights potential savings missed by not consistently purchasing the lowest fare. This `focus area` draws attention to cost inefficiencies in fare selection and helps identify areas where improved practices could yield significant financial benefits.

A `lost opportunity` is identified where a cheaper fare was offered but was not taken, where the cheaper fare offered meets the following criteria:

- The fare was not on a Low Cost Carrier
- The fare was in the same class as the fare taken
- The fare included baggage

and

- The travel time was within the client's nominated time period either side of the fare taken.

## Details
{: .no_toc}

1. TOC
{:toc}

## Metrics

- **True ticket count**: The sum count of `original tickets` purchased minus sum count of `refunds`.

- **Total spend**: The sum amount of `tickets` booked including taxes and GST.

- **Lost opportunity**: The `total spend` minus sum amount of `low fare tickets` presented.

- **Lost opportunity percentage**: The `lost opportunity` divided by the `total spend`.

## Dimensions

- **Break policy reason**: The reason that the `In-Policy` fare was not selected.

## Notes

Depending on how your policies are configured, there may still be `Lost Opportunity` values present for `In-Policy` bookings. This may occur when a policy specifies a tolerance for excess spend above the `Low Fare` (e.g. up to $100). Bookings with a spend inside the tolerance will be marked `In-Policy` and show a `Lost Opportunity`. For this reason, it is unlikely that `Lost Opportunity` will be exactly zero for `In-Policy` bookings.