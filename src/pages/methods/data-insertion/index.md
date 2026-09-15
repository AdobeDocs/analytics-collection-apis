---
title: Data Insertion API
description: Send event data directly to Adobe Analytics one hit at a time, as a query string (image request) or an XML POST.
keywords:
  - Data Insertion API
  - Image request
  - Hardcoded image request
  - Tracking pixel
  - Analytics data collection
---

# Data Insertion API

The Data Insertion API sends event data directly to Adobe Analytics collection servers without a client-side library such as AppMeasurement or the Web SDK. Those client-side data collection libraries use these same endpoints to send data to Adobe data collection servers. It is useful for server-side collection and for platforms that cannot run JavaScript.

You can send a hit in either of the following two encodings:

* **[Query string](request.md#query-string)**: An HTTP `GET` or `POST` whose data is a URL-encoded query string.
* **[XML](request.md#xml)**: An HTTP `POST` whose body is an XML payload.

The Data Insertion API sends one hit per request. For batch or high-volume collection (such as backfilling historical data or feeding an ETL pipeline), use the [Bulk Data Insertion API](../bulk-data-insertion/index.md), which accepts CSV files of many events. For a new server-side implementation, Adobe recommends using the Bulk Data Insertion API.

## How it works

Every hit is an HTTP request to a data collection endpoint that identifies the server, report suite, and response format:

```text
https://example.data.adobedc.net/b/ss/examplersid/1/s234234238479
```

A minimal hit, sent as a `GET` image request, looks like this:

```sh
curl "https://example.data.adobedc.net/b/ss/examplersid/1/s234234238479?AQB=1&g=http%3A%2F%2Fexample.com&pageName=Example%20direct%20hit&AQE=1"
```

Learn how to expand on this API:

* [Build a request](request.md): Contains the endpoint structure and required components for both query-string and XML encodings.
* [Response types](response-types.md): Response formats and how to read validation failures
* [Variable reference](variable-reference.md): Comprehensive table of every supported variable.
* [Visitor identification](https://experienceleague.adobe.com/en/docs/analytics/implementation/id/data-insertion): Learn the ideal architecture to manage visitor identification in the Analytics implementation guide.
