---
title: Bulk Data Insertion API column reference
description: Reference for the columns supported in Bulk Data Insertion API batch files.
---

# Bulk Data Insertion API column reference

Adobe supports the following columns in batch files. Each column maps to an Adobe Analytics variable or dimension. For file requirements, the two row modes, and examples, see [File format](file-format.md). For the `customerID` columns, see [Use a customer ID to identify visitors](customer-id.md).

The `queryString` column follows the same ingestion path as the [Data Insertion API](../data-insertion/index.md), so it accepts any query-string parameter that the Data Insertion API supports, not just the columns in the following table. For the complete parameter vocabulary, including short forms, XML tags, and retired variables, see the [Data Insertion API variable reference](../data-insertion/variable-reference.md).

<InlineAlert variant="info" slots="text"/>

The following fields can only be set with a column header; they have no `queryString` equivalent: `ipaddress`, `language`, `reportSuiteID`, `trackingServer`, and `userAgent`.

Column header name | Description
--- | ---
`aamlh` | Integer that represents the Adobe Audience Manager location hint. Valid values:\<ul\>\<li\>`3`: Hong Kong/Singapore (`apse.demdex.net`)\</li\>\<li\>`6`: Amsterdam/London (`irl1.demdex.net`)\</li\>\<li\>`7`: US Central/East (`use.demdex.net`)\</li\>\<li\>`8`: Australia (`apse2.demdex.net`)\</li\>\<li\>`9`: US West (`usw2.demdex.net`)\</li\>\<li\>`11`: Tokyo (`tyo3.demdex.net`)\</li\>\</ul\>
`browserHeight` | The [Browser height](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/browser-height) dimension.
`browserWidth` | The [Browser width](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/browser-width) dimension.
`campaign` | The [Tracking code](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/tracking-code) dimension.
`channel` | The [Site section](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/site-section) dimension.
`colorDepth` | The [Color depth](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/color-depth) dimension.
`connectionType` | The [Connection type](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/connection-type) dimension.
`contextData.key` | [`contextData`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/contextdata) implementation variables. Append your context data key to the column name, for example `contextData.color`.
`cookiesEnabled` | The [Cookie support](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/cookie-support) dimension.
`currencyCode` | The [`currencyCode`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/currencycode) implementation variable.
`customerID.[customerIDType].id` | The `id` used in the Visitor ID Service [`setCustomerIDs`](https://experienceleague.adobe.com/en/docs/id-service/using/id-service-api/methods/setcustomerids) method.
`customerID.[customerIDType].authState` | The `authState` used in the Visitor ID Service [`setCustomerIDs`](https://experienceleague.adobe.com/en/docs/id-service/using/reference/authenticated-state) method. String values are not case sensitive. Supported values:\<ul\>\<li\>`0`, `UNKNOWN`, or an empty string: Not logged in\</li\>\<li\>`1` or `AUTHENTICATED`: Logged in\</li\>\<li\>`2` or `LOGGED_OUT`: Logged out\</li\>\</ul\>
`customerID.[customerIDType].isMCSeed` | An integer boolean that lets you use `customerID.[customerIDType].id` as the hit's identifier. Use `1` for true and `0` for false. See [Use a customer ID to identify visitors](customer-id.md).
`eVar1` - `eVar250` | [eVar](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/evar) dimensions.
`events` | The [`events`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/events/events-overview) implementation variable.
`hier1` - `hier5` | [Hierarchy variables](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/hier). Retained only for legacy compatibility; not recommended for new implementations.
`hints.architecture` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): The underlying architecture for the device.
`hints.bitness` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): The "bitness" of the user-agent's CPU architecture; typically 64 or 32.
`hints.brands` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): List of browser brands and their significant version, formatted as a serialized JSON object array: `[{"brand":"Chromium","version":"104"}, {"brand":"Google Chrome","version":"104"}]`
`hints.mobile` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): Boolean indicating if the browser is on a mobile device.
`hints.model` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): The device model.
`hints.platform` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): The platform for the device, usually the operating system (OS).
`hints.platformversion` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): The version for the platform or OS.
`hints.wow64` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): Boolean indicating if a 32-bit user-agent application is running on a 64-bit Windows machine.
`ipaddress` | The visitor's IP address.
`javaEnabled` | The [Java enabled](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/java-enabled) dimension.
`language` | The [Language](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/language) dimension.
`linkName` | The [Download link](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/download-link), [Exit link](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/exit-link), or [Custom link](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/custom-link) dimension, depending on the value in the `linkType` column. If this column contains a value, `pageName` is ignored.
`linkType` | The type of link. Defaults to `o` (custom link) if this field is empty and `linkName` contains a value. Valid column values:\<ul\>\<li\>`d`: Download link\</li\>\<li\>`e`: Exit link\</li\>\<li\>`o`: Custom link\</li\>\</ul\>In the `queryString` column, use the query-string forms instead: `lnk_d`, `lnk_e`, or `lnk_o`.
`linkURL` | The link URL.
`list1` - `list3` | [List variables](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/list).
`marketingCloudVisitorID` | The unique identifier used with the [Adobe Visitor Identity Service](https://experienceleague.adobe.com/en/docs/id-service/using/home).
`pageName` | The [Page](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/page) dimension.
`pageType` | The [`pageType`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/pagetype) implementation variable. Set to the string value `"errorPage"` on any error pages, such as a 404 or 503 error.
`pageURL` | The [Page URL](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/page-url) dimension.
`products` | The [`products`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/products) implementation variable.
`prop1` - `prop75` | [Prop](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/prop) dimensions.
`purchaseID` | The [`purchaseID`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/purchaseid) implementation variable.
`queryString` | Key/value pairs that provide an alternative to using header columns. Accepts any parameter in the [Data Insertion API variable reference](../data-insertion/variable-reference.md). This column must be fully URL encoded, including any multi-byte characters. Adobe encodes the query string in UTF-8 by default.
`referrer` | The [`referrer`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/referrer) implementation variable.
`reportSuiteID` | Specifies the report suite(s) where you want to submit data. Separate multiple report suite IDs with a comma.
`resolution` | The [Monitor resolution](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/monitor-resolution) dimension.
`server` | The [Server](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/server) dimension.
`timestamp` | The date and time that the data was collected. [Unix Time](https://en.wikipedia.org/wiki/Unix_time) and [ISO 8601](https://datatracker.ietf.org/doc/html/rfc3339#section-5.6) are supported. Milliseconds are not allowed.
`tnta` | Target data payload. Used with [Analytics for Target](https://experienceleague.adobe.com/en/docs/target/using/integrate/a4t/a4t) integrations.
`trackingServer` | The [`trackingServer`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/trackingserver) implementation variable.
`transactionID` | The [`transactionID`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/transactionid) variable.
`userAgent` | The device's user agent string.
`visitorID` | The [`visitorID`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/visitorid) implementation variable.
`zip` | The [Zip code](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/zip-code) dimension.

The preceding table lists the only column headers that Adobe supports. If you upload a file with a column header that is not included in the table, that column is ignored. The `queryString` column is not subject to this restriction; it accepts any [Data Insertion API](../data-insertion/variable-reference.md) query-string parameter.
