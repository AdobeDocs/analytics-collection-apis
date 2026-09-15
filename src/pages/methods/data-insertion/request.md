---
title: Formulate a Data Insertion API request
description: How to construct a Data Insertion API request — the endpoint structure, required components, and the query-string and XML encodings.
keywords:
  - Data Insertion API
  - Image request
  - Query string
  - XML
  - Analytics data collection
---

# Build a Data Insertion API request

This page describes how to construct a valid Data Insertion API request using both query-string and XML encodings. The endpoint structure and components every hit must include are also listed here. For the formats the server responds with, see [Response types](response-types.md). For every supported variable, see the [variable reference](variable-reference.md).

## Request structure

Every hit is an HTTP request to the collection endpoint, which identifies the server, report suite, and response format:

```text
https://example.data.adobedc.net/b/ss/examplersid/1/s234234238479
```

* `https://` designates the protocol. Match the protocol the rest of your site uses (almost always HTTPS).
* `example.data.adobedc.net` is your data collection server. See [`trackingServerSecure`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/trackingserversecure) in the Analytics implementation guide to determine the correct value.
* `/b/ss/` is included in every request. It is part of the endpoint structure for Adobe data collection servers. <!-- Fun fact: `b` is short for beacon, and `ss` is short for SuperStats, the progenitor of Adobe Analytics -->
* `examplersid` is the report suite ID that receives the data. For multiple report suites, separate the IDs with commas and no spaces (such as `examplersid1,examplersid2`). When using [XML](#xml) encoding, you can set the report suite here in the path or in the `<reportSuiteID>` body element.
* `/1/` is the [response type](response-types.md). It selects the format of the server's response, such as a 1x1 GIF. Every response type accepts both `GET` and `POST`.
* `/s234234238479` (`"s"` followed by a random number) prevents the client from caching the request.

## Required components

Every hit must include:

* **A visitor identifier**: At least one of the following. When a hit carries more than one, Adobe applies a fixed [priority order](https://experienceleague.adobe.com/en/docs/analytics/implementation/id/overview):
  * Visitor ID override (`vid` / `<visitorId>`)
  * Analytics visitor ID (`aid` / `<analyticsVisitorId>`)
  * ECID (`mid` / `<marketingCloudVisitorId>`)
  * Fallback visitor ID (`fid` / `<fallbackVisitorId>`)
  * IP address and user agent (`<ipAddress>` and `<userAgent>`, supplied as HTTP headers when using query strings)
* **Page context**: At least one of:
  * Page name (`pageName` / `<pageName>`)
  * Page URL (`g` / `<pageUrl>`)
  * Link type (`pe` / `<linkType>`) with a link URL (`pev1` / `<linkUrl>`) or link name (`pev2` / `<linkName>`)
* **The report suite ID**: the endpoint path segment when using query strings; when using XML, the path segment or the `<reportSuiteID>` element (either works)

Hits that do not meet these requirements are omitted from reporting. When you send a `POST`, a hit missing one of these components fails validation with a reason you can read in the response — see [Validation and failures](response-types.md#validation-and-failures).

## Query string

When using query-string encoding (sometimes known as an **image request**), the data is a set of URL-encoded parameters:

```text
AQB=1&g=http%3A%2F%2Fexample.com&pageName=Example%20direct%20hit&v1=Example%20value&AQE=1
```

All values must be URL encoded. See the [variable reference](variable-reference.md) for every parameter, and the [FAQ](#faq) for encoding + length rules. You can send this payload as either a `GET` or a `POST`:

* **`GET`**: Append the payload to the endpoint as a query string to form a single URL.

  ```sh
  curl "https://example.data.adobedc.net/b/ss/examplersid/0/s234234238479?AQB=1&g=http%3A%2F%2Fexample.com&pageName=Example%20direct%20hit&v1=Example%20value&AQE=1"
  ```

  If using a response type of `/1/`, you can place it in an HTML `<img>` tag. Any client that loads images sends the hit:

  ```html
  <img src="https://example.data.adobedc.net/b/ss/examplersid/1/s234234238479?AQB=1&g=http%3A%2F%2Fexample.com&pageName=Example%20direct%20hit&v1=Example%20value&AQE=1"/>
  ```

  Using `GET` is the simplest form to construct, but it is subject to URL-length limits. Its data can also be recorded in browser, proxy, and server logs. A `GET` cannot return a validation failure reason — see [Validation and failures](response-types.md#validation-and-failures).

* **`POST`**: Send the endpoint as the request URL and the payload as the request body, using the `application/x-www-form-urlencoded` content type:

  ```sh
  curl -X POST "https://example.data.adobedc.net/b/ss/examplersid/1/s234234238479" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data "AQB=1&g=http%3A%2F%2Fexample.com&pageName=Example%20direct%20hit&v1=Example%20value&AQE=1"
  ```

  Because the payload travels in the body rather than the URL, using `POST` is not bound by URL-length limits, making it the better choice for large payloads. AppMeasurement switches to `POST` automatically whenever the request URL reaches 2048 characters.

  <InlineAlert variant="info" slots="text"/>

  If you send a `POST` from a browser with `XMLHttpRequest` or `fetch`, include credentials ([`withCredentials = true`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/withCredentials) or [`credentials: "include"`](https://developer.mozilla.org/en-US/docs/Web/API/Request/credentials)) so the request carries the visitor's Analytics opt-out cookie. The collection server is a different origin from your page, and browsers omit cookies on cross-origin `XMLHttpRequest`/`fetch` requests unless credentials are enabled. If credentials are not included, an opted-out visitor could still be tracked. A `GET` request from an `<img>` tag sends those cookies automatically, and a server-side `POST` has no visitor cookies to send.

## XML

When using XML encoding, the data is an XML document sent as the body of an HTTP `POST` with the `application/xml` content type. XML request bodies are parsed only at the `/6/` [response type](response-types.md) — sending an XML body to any other response type causes it to be parsed as a query string, which fails. Set the report suite in the `<reportSuiteID>` element or in the [endpoint](#request-structure) path.

See the [variable reference](variable-reference.md) for every supported XML tag. Note the following XML formatting rules:

* Each supported element carries a single text value. If an element contains mixed content (nested elements alongside text), only the first child's text is used.
* Only the standard XML entities are supported (`&amp;`, `&lt;`, `&gt;`, `&quot;`, `&apos;`). DOCTYPE declarations and custom or external entities (DTDs) are stripped and not processed.

<CodeBlock slots="heading, code" repeat="2" languages="CURL,XML"/>

#### Request

```sh
curl -X POST "https://example.data.adobedc.net/b/ss//6" \
    -H "Accept: application/xml" \
    -H "Content-Type: application/xml" \
    -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?>
        <request>
            <pageURL>https://example.com</pageURL>
            <pageName>Data Insertion API test (XML POST)</pageName>
            <reportSuiteID>examplersid</reportSuiteID>
        </request>"
```

#### Response

```xml
<?xml version="1.0" encoding="UTF-8"?>
<status>SUCCESS</status>
```

For the `FAILURE` responses and how to resolve them, see [Validation and failures](response-types.md#validation-and-failures).

## Visitor identification

Because you build each hit yourself, you set the visitor identifier rather than relying on a library to manage it. Adobe data collection servers always attempt to set a cookie containing the visitor identifier. Some [response types](response-types.md) (`/10/` and `/11/`) include the visitor identifier in the response as well. For the full client-side and server-side patterns, see [Visitor identification using the Data Insertion API](https://experienceleague.adobe.com/en/docs/analytics/implementation/id/data-insertion).

## FAQ

Common questions about building hits directly.

<AccordionItem slots="heading, text"/>

### Is JSON supported?

No. The Data Insertion API accepts data only as a URL-encoded query string or an XML body. To collect data as JSON, use the [Adobe Experience Platform Web SDK](https://experienceleague.adobe.com/en/docs/experience-platform/web-sdk/home) or the [Data Collection APIs](https://developer.adobe.com/data-collection-apis/docs/) instead.

<AccordionItem slots="heading, text"/>

### Are query string parameters case-sensitive?

**Yes, query string parameters are case-sensitive.** Make sure that query string parameters exactly match, or else they are not recorded. For example, `pagename` is not a valid query string parameter, while `pageName` is.

<AccordionItem slots="heading, text"/>

### Are XML tags case-sensitive?

**No, XML tags are not case-sensitive.** For example, `<pageName>`, `<pagename>`, and `<PAGENAME>` are all valid.

<AccordionItem slots="heading, text, text"/>

### Can I include spaces in the query string?

Values for each of the query string parameters are URL encoded. URL encoding converts characters that are normally illegal in URLs into legal characters. For example, a space character is converted into `%20`. Make sure that any character that is not alphanumeric is URL encoded. Adobe automatically URL decodes values when requests reach data collection servers.

See [HTML URL Encoding Reference](https://www.w3schools.com/tags/ref_urlencode.asp) on W3Schools for more information on how URL encoding works.

<AccordionItem slots="heading, text"/>

### What is the maximum number of characters a single value can have?

Each variable has a different maximum length. Most traffic variables hold up to 100 bytes, while most conversion variables hold up to 255 bytes. When a request reaches data collection servers, Adobe automatically truncates these values to their maximum length.

<AccordionItem slots="heading, text"/>

### How long does data take to appear in reporting?

Data submitted through the Data Insertion API follows the standard Adobe Analytics [latency](https://experienceleague.adobe.com/en/docs/analytics/technotes/latency) process.

<AccordionItem slots="heading, text, text, text"/>

### Can I track email opens with an image request?

Yes. A query-string `GET` resolves to a 1x1 transparent pixel, so any email client that loads external images fires the hit when it renders the message. This behavior works the same way across clients; it does not depend on a specific mail application.

The practical way to add the pixel is through an email service provider, which injects tracking pixels into your HTML automatically, or by sending the HTML yourself over SMTP or a sending API. Hand-composing in a mail client is fragile: older Outlook desktop versions required embedding the raw HTML through the **Insert as Text** option, and webmail composers such as Gmail sanitize pasted HTML.

Open tracking through images is far less reliable than it once was, which is why it is now a niche technique. Apple Mail Privacy Protection pre-fetches every remote image whether or not the recipient opens the message, which inflates open counts. Webmail image proxies (such as Gmail and Outlook.com) route the request through their own servers and cache it, so the IP address, user agent, and cookies belong to the proxy rather than the recipient. Clients that block external images by default do not record an open until the recipient displays them, and every render can increment a billable server call.
