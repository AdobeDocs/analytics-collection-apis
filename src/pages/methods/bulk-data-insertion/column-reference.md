---
title: Bulk Data Insertion API column reference
description: Reference for the columns and query-string parameters supported in Bulk Data Insertion API batch files.
---

# Bulk Data Insertion API column reference

Adobe supports the following columns in batch files. Each column maps to an Adobe Analytics variable or dimension, and most have a `queryString` equivalent that you can use inside the `queryString` column. For file requirements, the two row modes, and examples, see [File format](file-format.md). For the `customerID` columns, see [Use a customer ID to identify visitors](customer-id.md).

Column header name | `queryString` equivalent | Description
--- | --- | ---
`aamlh` | `aamlh` | Integer that represents the Adobe Audience Manager location hint. Valid values:\<br/\>• `3` — Hong Kong/Singapore (`apse.demdex.net`)\<br/\>• `6` — Amsterdam/London (`irl1.demdex.net`)\<br/\>• `7` — US Central/East (`use.demdex.net`)\<br/\>• `8` — Australia (`apse2.demdex.net`)\<br/\>• `9` — US West (`usw2.demdex.net`)\<br/\>• `11` — Tokyo (`tyo3.demdex.net`)
`browserHeight` | `bh` | The [Browser height](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/browser-height) dimension.
`browserWidth` | `bw` | The [Browser width](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/browser-width) dimension.
`campaign` | `v0` | The [Tracking code](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/tracking-code) dimension.
`channel` | `ch` | The [Site section](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/site-section) dimension.
`colorDepth` | `c` | The [Color depth](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/color-depth) dimension.
`connectionType` | `ct` | The [Connection type](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/connection-type) dimension.
`contextData.key` | `c.[key]` | [`contextData`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/contextdata) implementation variables.
`cookiesEnabled` | `k` | The [Cookie support](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/cookie-support) dimension.
`currencyCode` | `cc` | The [`currencyCode`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/currencycode) implementation variable.
`customerID.[customerIDType].id` | `cid.[customerIDType].id` | The `id` used in the Visitor ID Service [`setCustomerIDs`](https://experienceleague.adobe.com/en/docs/id-service/using/id-service-api/methods/setcustomerids) method.
`customerID.[customerIDType].authState` | `cid.[customerIDType].as` | The `authState` used in the Visitor ID Service [`setCustomerIDs`](https://experienceleague.adobe.com/en/docs/id-service/using/reference/authenticated-state) method. String values are not case sensitive. Supported values:\<br/\>• `0`, `UNKNOWN`, or an empty string — not logged in\<br/\>• `1` or `AUTHENTICATED` — logged in\<br/\>• `2` or `LOGGED_OUT` — logged out
`customerID.[customerIDType].isMCSeed` | `cid.[customerIDType].ismcseed` | An integer boolean that lets you use `customerID.[customerIDType].id` as the hit's identifier. Use `1` for true and `0` for false. See [Use a customer ID to identify visitors](customer-id.md).
`eVar1` - `eVar250` | `v1` - `v250` | [eVar](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/evar) dimensions.
`events` | `events` | The [`events`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/events/events-overview) implementation variable.
`hier1` - `hier5` | `h1` - `h5` | [Hierarchy variables](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/hier).
`hints.architecture` | `h.architecture` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): The underlying architecture for the device.
`hints.bitness` | `h.bitness` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): The "bitness" of the user-agent's CPU architecture — typically 64 or 32.
`hints.brands` | `h.brands` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): List of browser brands and their significant version, formatted as a serialized JSON object array: `[{"brand":"Chromium","version":"104"}, {"brand":"Google Chrome","version":"104"}]`
`hints.mobile` | `h.mobile` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): Boolean indicating if the browser is on a mobile device.
`hints.model` | `h.model` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): The device model.
`hints.platform` | `h.platform` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): The platform for the device, usually the operating system (OS).
`hints.platformversion` | `h.platformversion` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): The version for the platform or OS.
`hints.wow64` | `h.wow64` | [Client Hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints): Boolean indicating if a 32-bit user-agent application is running on a 64-bit Windows machine.
`ipaddress` | N/A (only available with column header) | The visitor's IP address.
`javaEnabled` | `v` | The [Java enabled](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/java-enabled) dimension.
`language` | N/A (only available with column header) | The [Language](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/language) dimension.
`linkName` | `pev2` | The [Download link](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/download-link), [Exit link](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/exit-link), or [Custom link](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/custom-link) dimension, depending on the value in the `linkType` column. If this column contains a value, `pageName` is ignored.
`linkType` | `pe` | The type of link. Defaults to `o` if this field is empty and `linkName` contains a value. Valid values when using the `linkType` column:\<br/\>• `d` — download link\<br/\>• `e` — exit link\<br/\>• `o` — custom link\<br/\>When using the `pe` query string, use:\<br/\>• `lnk_d` — download link\<br/\>• `lnk_e` — exit link\<br/\>• `lnk_o` — custom link
`linkURL` | `pev1` | The link URL.
`list1` - `list3` | `l1` - `l3` | [List variables](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/list).
`marketingCloudVisitorID` | `mid` | The unique identifier used with the [Adobe Experience Cloud Identity Service](https://experienceleague.adobe.com/en/docs/id-service/using/home).
`pageName` | `pageName` | The [Page](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/page) dimension.
`pageType` | `pageType` | The [`pageType`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/pagetype) implementation variable. Set to the string value `"errorPage"` on any error pages, such as a 404 or 503 error.
`pageURL` | `g` | The [Page URL](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/page-url) dimension.
`products` | `products` | The [`products`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/products) implementation variable.
`prop1` - `prop75` | `c1` - `c75` | [Prop](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/prop) dimensions.
`purchaseID` | `purchaseID` | The [`purchaseID`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/purchaseid) implementation variable.
`queryString` | This column provides information for this field. | Key/value pairs that provide an alternative to using header columns. This column must be fully URL encoded, including any multi-byte characters. Adobe encodes the query string in UTF-8 by default.
`referrer` | `r` | The [`referrer`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/referrer) implementation variable.
`reportSuiteID` | N/A (only available with column header) | Specifies the report suite(s) where you want to submit data. Separate multiple report suite IDs with a comma.
`resolution` | `s` | The [Monitor resolution](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/monitor-resolution) dimension.
`server` | `server` | The [Server](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/server) dimension.
`timestamp` | `ts` | The date and time that the data was collected. [Unix Time](https://en.wikipedia.org/wiki/Unix_time) and [ISO 8601](https://datatracker.ietf.org/doc/html/rfc3339#section-5.6) are supported. Milliseconds are not allowed.
`tnta` | `tnta` | Target data payload. Used with [Analytics for Target](https://experienceleague.adobe.com/en/docs/target/using/integrate/a4t/a4t) integrations.
`trackingServer` | N/A (only available with column header) | The [`trackingServer`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/trackingserver) implementation variable.
`transactionID` | `xact` | The [`transactionID`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/transactionid) variable.
`userAgent` | N/A (only available with column header) | The device's user agent string.
`visitorID` | `vid` | The [`visitorID`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/visitorid) implementation variable.
`zip` | `zip` | The [Zip code](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/zip-code) dimension.

The preceding table lists the only column headers that Adobe supports. If you upload a file with a column header that is not included in the table, that column is ignored.
