---
title: Data Insertion API response types
description: The Data Insertion API response types (GIF, XML, JSON, and more) and how Adobe Analytics data collection surfaces hit validation failures.
keywords:
  - Data Insertion API
  - Response type
  - Status
  - Reason
  - Analytics data collection
  - ECID
---

# Data Insertion API response types

The response type is the numeric segment of the [endpoint path](request.md#request-structure) (the `/1/` in `/b/ss/examplersid/1/s234234238479`). It selects the **format** of the server's response: a GIF, `204 No Content`, JavaScript, JSON, XML, and so on. The response format is independent of how you send the request, with one exception: an XML request body is parsed only at the `/6/` response type (see [XML](request.md#xml)).

Every response type technically accepts both `GET` and `POST`; however, several response types would not make sense in practice. The type you choose does not change whether a valid hit is recorded; it changes only the response you receive and, for some types, how a validation failure is surfaced.

## Response types

<AccordionItem slots="heading, text"/>

### /0/: HTML

Returns a single space with a `text/html` content type, and `200 OK`. On a failed hit it sets a `Status: FAILURE` header (with a `Reason` header when a reason is available), and it flags failure even when no hit is recorded, so it surfaces a dropped `GET`.

<AccordionItem slots="heading, text"/>

### /1/: GIF (default)

Returns a 1x1 transparent GIF (`image/gif`), and `200 OK`. Use this response type for `<img>` tag requests. AppMeasurement primarily uses this response type for both `GET` and `POST` requests. On `POST` validation failures it sets `Status` and `Reason` headers; `GET` validation surfaces nothing, even when the hit is dropped.

<AccordionItem slots="heading, text"/>

### /2/: No content

Returns `204 No Content` with no body. It sets a `Status` header (`SUCCESS` or `FAILURE`) on any method, plus a `Reason` header when a reason is available (the most reliable lightweight signal).

<AccordionItem slots="heading, text, code"/>

### /3/: JavaScript

Returns a JavaScript response that assigns the Analytics visitor ID to a `s_vid` variable, so the ID can be read after the beacon fires. This ID is the visitor's `s_vi` cookie value (the `aid` [variable](variable-reference.md)). This response type returns only the Analytics visitor ID, never the ECID (`mid`), so the `mid` and `mcorgid` parameters have no effect on it. A request identified only by an ECID resolves no Analytics visitor ID, so the response body is empty. On `POST` validation failure it sets `Status` and `Reason` headers.

```js
var s_vid='355231C82E332200-4000195842CEFA67'
```

<AccordionItem slots="heading, text"/>

### /4/: Partner redirect

Used by select partner libraries, and only on `GET` requests. Do not set it manually.

<AccordionItem slots="heading, text"/>

### /5/: WBMP

An `image/wbmp` image, equivalent to `/1/` (GIF) for legacy `wbmp`-only clients. It does not surface a status or reason.

<AccordionItem slots="heading, text, code"/>

### /6/: XML

Returns an XML body with the hit status. This response type exclusively parses an XML request body (see [XML](request.md#xml)). It also mirrors the status and reason into `Status`/`Reason` headers, except for a `NO account` failure, which appears only in the body.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<status>SUCCESS</status>
```

<AccordionItem slots="heading, text, text, code"/>

### /10/: Visitor JSON

Returns the hit status and visitor identifiers as JSON. AppMeasurement uses this response type when Adobe Audience Manager is included in your implementation.

The `id` is the visitor's `s_vi` cookie value (the `aid` [variable](variable-reference.md)). The response also returns the ECID (`mid`) as a 38-digit decimal string, but only when the request includes both a valid `mid` and `mcorgid` query parameters. If either component is missing, the server returns the `aid` alone. Status appears in the JSON body, not in headers, and a hit that fails validation returns without an `id`.

<CodeBlock slots="heading, code" repeat="2" languages="JSON,JSON"/>

#### With ECID

```json
{
  "status":"SUCCESS",
  "mid":"79616681662205094719519402412079274310",
  "id":"355231C82E332200-4000195842CEFA67"
}
```

#### Analytics ID only

```json
{
  "status":"SUCCESS",
  "id":"355231C82E332200-4000195842CEFA67"
}
```

<AccordionItem slots="heading, text, text, code"/>

### /11/: Visitor XML

The same visitor identifiers as `/10/`, returned as XML. The `id` is the visitor's `s_vi` [cookie](https://experienceleague.adobe.com/en/docs/core-services/interface/data-collection/cookies/analytics) value (the `aid` [variable](variable-reference.md)), and the `mid` is the ECID as a 38-digit decimal string. The `mid` appears only when the request supplies both a valid `mid` and `mcorgid` as query parameters.

Status appears in the `<visitor>` body, not in headers, and a hit that fails validation returns an empty `<visitor>` element. Reading it back is the basis of the server-side identity pattern in [Visitor identification using the Data Insertion API](https://experienceleague.adobe.com/en/docs/analytics/implementation/id/data-insertion).

<CodeBlock slots="heading, code" repeat="2" languages="XML,XML"/>

#### With ECID

```xml
<?xml version="1.0" encoding="UTF-8"?>
<visitor>
  <status>SUCCESS</status>
  <mid>79616681662205094719519402412079274310</mid>
  <id>355231C82E332200-4000195842CEFA67</id>
</visitor>
```

#### Analytics ID only

```xml
<?xml version="1.0" encoding="UTF-8"?>
<visitor>
  <status>SUCCESS</status>
  <id>355231C82E332200-4000195842CEFA67</id>
</visitor>
```

## Validation and failures

Whatever the response type, a `2xx` status confirms only that the request was *received*, not that the hit passed validation or appeared in reporting. How a failure is surfaced depends on the HTTP method and the response type: a failure **reason** is computed only for `POST` requests, so `GET` responses never return one. The surest confirmation is to inspect the request with a [packet monitor](https://experienceleague.adobe.com/en/docs/analytics/implementation/validate/packet-monitor) as you send it, or to check for the data in Adobe Analytics reporting.

A failed hit reports one of the following reasons. The same reason string appears wherever the response type surfaces it; a `Reason` header, the `<reason>` XML element, or the JSON body.

| Reason | Meaning |
|---|---|
| `NO account` | Missing the required report suite ID. |
| `NO pagename OR pageurl` | Missing the required page name or page URL. |
| `NO visitorid OR ipaddress` | Missing the required visitor identifier. |
| `Syntax error` | Malformed XML, or a reserved character that was not encoded. |

### Statuses and reasons by response type

The reason value is the same everywhere; only the container differs by response type and HTTP method. A reason is computed only for `POST` requests, so every `GET` cell below is either `Nothing` or a bare `Status`.

| Response type | On `GET` | On `POST` | Notes |
|---|---|---|---|
| `/0/` HTML | `Status` header | `Status` + `Reason` headers | Failure only; also flags a dropped hit. |
| `/1/` GIF | Nothing | `Status` + `Reason` headers | Failure only. |
| `/2/` No content | `Status` header | `Status` + `Reason` headers | `SUCCESS` or `FAILURE`; also flags a dropped hit. |
| `/3/` JavaScript | Nothing | `Status` + `Reason` headers | Failure only. |
| `/4/` Partner redirect | Nothing | N/A | Partner use only; don't set manually. |
| `/5/` WBMP | Nothing | Nothing | Surfaces nothing. |
| `/6/` XML | N/A | `<status>`/`<reason>` body + headers | Header suppressed for `NO account` (still in the body). |
| `/10/` Visitor JSON | `status` in body | `status` in body | No headers; no `id` on failure. |
| `/11/` Visitor XML | `<status>` in body | `<status>` in body | No headers; empty `<visitor>` on failure. |

### Resolving failures

If hits return `FAILURE`, check the following:

* **Encoding.** When using XML encoding, make sure that the content is UTF-8 and that you're using a response type of `/6/`. When using query-string encoding, make sure that all values are properly URL-encoded.
* **Reserved characters.** In XML, replace ampersands (`&`), greater-than (`>`), and less-than (`<`) with their entities when they appear inside a value. For example, submit `News & Sports <local>` as `News &amp; Sports &lt;local&gt;`. In query strings, these characters are handled by URL encoding.
* **Required components.** Confirm that the hit includes a report suite ID, page context, and a visitor identifier. See [Required components](request.md#required-components).

### POST request rejected

Some HTTP clients add an `Expect: 100-Continue` header to `POST` requests, which Adobe data collection servers reject. The request fails before the body is processed, so no `FAILURE` body is returned. Disable the header in your client. For example, in .NET, set `ServicePointManager.Expect100Continue = false`.
