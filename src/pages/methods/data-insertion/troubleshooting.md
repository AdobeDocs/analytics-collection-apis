---
title: Troubleshoot Data Insertion API requests
description: Diagnose rejected requests, failed or dropped hits, and incorrectly recorded data, including HTTP response codes, for the Data Insertion API.
keywords:
  - Data Insertion API
  - Troubleshooting
  - HTTP status code
  - Analytics data collection
---

# Troubleshoot Data Insertion API requests

Use the following sections to determine why a request was rejected, why a hit failed or was dropped, or why data was recorded differently than you expected. To see exactly what you sent and what the server returned, inspect the request with a [packet analyzer](https://experienceleague.adobe.com/en/docs/analytics/implementation/validate/packet-monitor).

## HTTP response codes

Data collection servers return the following HTTP status codes. A `2xx` code confirms only that the server received the request, not that the hit passed validation. See [Hit returns a failure status](#hit-returns-a-failure-status).

| HTTP response code | Description | Client action |
|---|---|---|
| `200` | OK. The server received the request. Returned by response types that return a body, including the default `/1/` GIF. | None. |
| `204` | No content. The server received the request. Returned by the `/2/` [response type](response-types.md). | None. |
| `302` | Found. Data collection servers can redirect a new visitor's first request to check that the visitor cookie was set. The redirected request includes the `pccr` and `vidn` [variables](variable-reference.md). Requests that use the `/4/` partner redirect response type also redirect. | None. A redirect does not indicate a failure. |
| `404` | Not found. The request path does not match a data collection endpoint, often because a hard-coded request URL is malformed. The hit is not recorded. | Check the [request structure](request.md#request-structure). |
| `5xx` | Server error. The server could not process the request. | If the error persists, check [Adobe Status](https://status.adobe.com/en/experience_cloud), then contact Adobe Customer Care with the request and response details. |

## POST request rejected

Some HTTP clients add an `Expect: 100-Continue` header to `POST` requests, which Adobe data collection servers reject. The request fails before the body is processed, so no `FAILURE` body is returned. Disable the header in your client. For example, in .NET, set `ServicePointManager.Expect100Continue = false`.

## Hit returns a failure status

A hit that fails validation reports `FAILURE` with one of the following reasons. A reason is computed only for `POST` requests, and each response type reports it in a different place. See [Statuses and reasons by response type](response-types.md#statuses-and-reasons-by-response-type).

| Reason | Meaning |
|---|---|
| `NO account` | Missing the required report suite ID. |
| `NO pagename OR pageurl` | Missing the required page name or page URL. |
| `NO visitorid OR ipaddress` | Missing the required visitor identifier. |
| `Syntax error` | Malformed XML, or a reserved character that was not encoded. |

### Resolving failures

If hits return `FAILURE`, check the following:

* **Required components.** Confirm that the hit includes a report suite ID, page context, and a visitor identifier. See [Required components](request.md#required-components).
* **Encoding.** When using XML encoding, make sure that the content is UTF-8 and that you're using a response type of `/6/`. When using query-string encoding, make sure that all values are properly URL-encoded.
* **Reserved characters.** In XML, replace ampersands (`&`), greater-than (`>`), and less-than (`<`) with their entities when they appear inside a value. For example, submit `News & Sports <local>` as `News &amp; Sports &lt;local&gt;`. In query strings, these characters are handled by URL encoding.

## Data missing from reports

If the server accepted the request but the data does not appear in reporting, check the following causes.

### Data not processed yet

Data submitted through the Data Insertion API follows the standard Adobe Analytics [latency](https://experienceleague.adobe.com/en/docs/analytics/technotes/latency) process. Allow for normal latency before investigating other causes.

### Dropped GET hits return success

With the default `/1/` response type, a `GET` request returns `200 OK` and a GIF even when the hit is dropped, so the response alone cannot confirm that the hit was recorded. While testing, send `GET` requests to `/0/` or `/2/`, which set a `Status` header that also flags a dropped hit, or send a `POST` request to receive a failure reason. See [Statuses and reasons by response type](response-types.md#statuses-and-reasons-by-response-type).

### Timestamp does not match the report suite setting

A report suite's timestamp configuration determines whether hits can include the `ts` [variable](variable-reference.md):

* **Timestamps not allowed**: Hits that include `ts` are permanently dropped.
* **Timestamps required**: Hits without `ts` are permanently dropped.
* **Timestamps optional**: Hits are accepted with or without `ts`.

See [Timestamps optional](https://experienceleague.adobe.com/en/docs/analytics/technotes/timestamps-optional) in the Adobe Analytics documentation.

### Invalid currency code

If the `cc` (currency code) variable is set to an invalid value, the entire hit is discarded. See [`currencyCode`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/currencycode) in the Adobe Analytics documentation.

### Parameter has the wrong case

Query string parameters are case-sensitive. A parameter that does not exactly match is not recorded. For example, `pagename` is not a valid query string parameter, while `pageName` is. Check parameter names against the [variable reference](variable-reference.md).

XML tags are not case-sensitive. For example, `<pageName>`, `<pagename>`, and `<PAGENAME>` are all valid.

### Long GET request truncated

`GET` requests are subject to URL-length limits, so a request with many or long values can be truncated. Send large payloads as `POST`, which is not bound by URL-length limits. AppMeasurement switches to `POST` automatically whenever the request URL reaches 2048 characters. See [Query string](request.md#query-string).

## Data recorded incorrectly

If data appears in reporting but does not match what you expected, check the following causes.

### Values are truncated

Each variable has a different maximum length. Most traffic variables hold up to 100 bytes, while most conversion variables hold up to 255 bytes. When a request reaches data collection servers, Adobe automatically truncates these values to their maximum length. See [Data column reference](https://experienceleague.adobe.com/en/docs/analytics/export/analytics-data-feed/data-feed-contents/datafeeds-reference) for a comprehensive list of data types for each variable.

### Location and device data reflect your server

A server-side request carries no visitor cookies, and its own IP address and user agent belong to the sender. To attribute hits correctly, forward the visitor's real IP address (the `X-Forwarded-For` header) and user agent (the `User-Agent` header). See [Visitor identification using the Data Insertion API](https://experienceleague.adobe.com/en/docs/analytics/implementation/id/data-insertion).

### Visitor counts are too low

If the `vid` variable contains a default or placeholder value (such as `"0"` or `"NULL"`), Adobe treats those hits as the same visitor. Visitor counts are artificially low, and visitor-level segments do not work as expected. Incorrect custom visitor IDs can also increase processing [latency](https://experienceleague.adobe.com/en/docs/analytics/technotes/latency). Send `vid` only when you can guarantee a stable value on every hit. See [`visitorID`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/visitorid) in the Adobe Analytics documentation.

### Timestamped hits are out of order

Hits that are received out of order per visitor can produce unexpected reporting results, so send timestamped hits in order per visitor. If the report suite uses **Timestamps optional**, do not set `vid` on timestamped hits. Doing so can lead to out-of-order data and negatively affect time spent, attribution, visit number, visit counts, and pathing. See [Timestamps optional](https://experienceleague.adobe.com/en/docs/analytics/technotes/timestamps-optional) in the Adobe Analytics documentation.
