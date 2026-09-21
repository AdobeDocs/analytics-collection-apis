---
title: Data Insertion API response types
description: The Data Insertion API response types (GIF, XML, JSON, and more) and how Adobe Analytics data collection surfaces hit validation failures.
keywords:
  - Data Insertion API
  - Response type
  - Status
  - Reason
  - Analytics data collection
---

# Data Insertion API response types

The response type is the numeric segment of the [endpoint path](request.md#request-structure) — the `/1/` in `/b/ss/examplersid/1/s234234238479`. It selects the **format** of the server's response: a GIF, `204 No Content`, JavaScript, JSON, XML, and so on. The response format is independent of how you send the request, with one exception — an XML request body is parsed only at the `/6/` response type (see [XML](request.md#xml)).

Every response type accepts both `GET` and `POST`. The type you choose does not change whether a valid hit is recorded; it changes only the response you receive and, for some types, how a validation failure is surfaced.

## Response types

<AccordionItem slots="heading, text"/>

### /0/ — HTML

Returns a single space with a `text/html` content type, and `200 OK`. On a failed hit it sets a `Status: FAILURE` header (with a `Reason` header when a reason is available), and it flags failure even when no hit is recorded — so it surfaces a dropped `GET`. See [Validation and failures](#validation-and-failures).

<AccordionItem slots="heading, text"/>

### /1/ — GIF (default)

Returns a 1x1 transparent GIF (`image/gif`), and `200 OK`. Use this response type for `<img>` tag requests. AppMeasurement uses it by default (both `GET` and `POST`). On a `POST` validation failure it sets `Status` and `Reason` headers; a `GET` surfaces nothing, even when the hit is dropped. See [Validation and failures](#validation-and-failures).

<AccordionItem slots="heading, text"/>

### /2/ — No content

Returns `204 No Content` with no body. It sets a `Status` header (`SUCCESS` or `FAILURE`) on any method, plus a `Reason` header when a reason is available — the most reliable lightweight signal. See [Validation and failures](#validation-and-failures).

<AccordionItem slots="heading, text, code"/>

### /3/ — JavaScript

Returns a JavaScript response that assigns the visitor ID to the `s_vid` variable, so a browser can read the ID back after the beacon fires. On a `POST` validation failure it sets `Status` and `Reason` headers.

```js
var s_vid='355231C82E332200-4000195842CEFA67'
```

<AccordionItem slots="heading, text"/>

### /4/ — Partner redirect

Used by select partner libraries, and only on a `GET`. Do not set it manually.

<AccordionItem slots="heading, text"/>

### /5/ — WBMP

An `image/wbmp` image, equivalent to `/1/` (GIF) for legacy `wbmp`-only clients. It does not surface a status or reason.

<AccordionItem slots="heading, text, code, text"/>

### /6/ — XML

Returns an XML body with the hit status. This is the only response type that parses an XML request body (see [XML](request.md#xml)); it also mirrors the status and reason into `Status`/`Reason` headers, except for a `NO account` failure, which appears only in the body.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<status>SUCCESS</status>
```

See [Validation and failures](#validation-and-failures) for the `FAILURE` bodies.

<AccordionItem slots="heading, text, code, text"/>

### /10/ — Visitor JSON

Returns the hit status and visitor ID as JSON. When an Experience Cloud ID (`mid`) is available for the visitor, the response includes additional visitor information as well. AppMeasurement uses this response type when Audience Manager is included in your implementation.

```json
{"status":"SUCCESS","id":"355231C82E332200-4000195842CEFA67"}
```

The `id` is the visitor's `s_vi` cookie value (the `aid` [variable](variable-reference.md)). Status appears in the JSON body, not in headers; a hit that fails validation returns without an `id`.

<AccordionItem slots="heading, text, code, text"/>

### /11/ — Visitor XML

The same visitor information as `/10/`, returned as XML.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<visitor>
  <status>SUCCESS</status>
  <id>355231C82E332200-4000195842CEFA67</id>
</visitor>
```

The `id` is the visitor's `s_vi` [cookie](https://experienceleague.adobe.com/en/docs/core-services/interface/data-collection/cookies/analytics) value (the `aid` [variable](variable-reference.md)). Status appears in the `<visitor>` body, not in headers; a hit that fails validation returns an empty `<visitor>` element. Reading it back is the basis of the server-side identity pattern in [Visitor identification using the Data Insertion API](https://experienceleague.adobe.com/en/docs/analytics/implementation/id/data-insertion).

## Validation and failures

Whatever the response type, a `2xx` status confirms only that the request was *received*, not that the hit passed validation or appeared in reporting. How a failure is surfaced depends on the HTTP method and the response type: a failure **reason** is computed only for `POST` requests, so a `GET` never returns one. The surest confirmation is to inspect the request with a [packet monitor](https://experienceleague.adobe.com/en/docs/analytics/implementation/validate/packet-monitor) as you send it, or to check for the data in Adobe Analytics reporting.

### Failure reasons

A failed hit reports one of the following reasons. The same reason string appears wherever the response type surfaces it — a `Reason` header, the `<reason>` XML element, or the JSON body.

| Reason | Meaning |
|---|---|
| `NO account` | Missing the required report suite ID. |
| `NO pagename OR pageurl` | Missing the required page name or page URL. |
| `NO visitorid OR ipaddress` | Missing the required visitor identifier. |
| `Syntax error` | Malformed XML, or a reserved character that was not encoded. |

For which components are required, see [Required components](request.md#required-components).

### How each response type surfaces status

The reason value is the same everywhere; only the container differs by response type. On a `GET`, no reason is ever returned, though `/0/` and `/2/` still report a bare `Status`.

| Response type | Status | Reason | Notes |
|---|---|---|---|
| `/0/` HTML | `Status` header | `Reason` header | Failure only; also flags failure when no hit is recorded, so it catches a dropped `GET`. |
| `/1/` GIF | `Status` header | `Reason` header | Only on a `POST` failure — a `GET` surfaces nothing, even when dropped. |
| `/2/` No content | `Status` header | `Reason` header | Emits `SUCCESS` too, and flags failure when no hit is recorded. Works on any method. |
| `/3/` JavaScript | `Status` header | `Reason` header | Failure only. |
| `/4/` Partner redirect | `Status` header | `Reason` header | Partner use only; `GET` only. |
| `/5/` WBMP | — | — | Surfaces nothing. |
| `/6/` XML | `<status>` body + `Status` header | `<reason>` body + `Reason` header | Header suppressed for `NO account` (still in the body). |
| `/10/` Visitor JSON | `status` field | in body | No headers; returns without an `id` on failure. |
| `/11/` Visitor XML | `<status>` body | `<reason>` body | No headers; returns an empty `<visitor>` on failure. |

### Resolving failures

If hits return `FAILURE`, check the following:

* **Encoding.** In the XML encoding, make sure the content is UTF-8. In the query-string encoding, URL-encode every value.
* **Reserved characters.** In XML, replace ampersands (`&`), greater-than (`>`), and less-than (`<`) with their entities when they appear inside a value. For example, submit `News & Sports <local>` as `News &amp; Sports &lt;local&gt;`. In the query string, these characters are handled by URL encoding.
* **Required components.** Confirm the hit includes a report suite ID, page context, and a visitor identifier. See [Required components](request.md#required-components).

### POST request rejected

Some HTTP clients add an `Expect: 100-Continue` header to `POST` requests, which Adobe data collection servers reject — the request fails before the body is processed, so no `FAILURE` body is returned. Disable the header in your client. For example, in .NET, set `ServicePointManager.Expect100Continue = false`.
