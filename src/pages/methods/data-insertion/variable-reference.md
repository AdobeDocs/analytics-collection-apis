---
title: Data Insertion API variable reference
description: Every variable the Data Insertion API supports, with its query-string parameter and XML tag forms.
---

# Data Insertion API variable reference

This reference lists every variable that the Data Insertion API supports, in both XML and query string encodings.

Adobe data collection servers only process supported variables; anything unrecognized is ignored. XML tags are not case-sensitive; query parameters are.

For the components every hit must include, see [Required components](request.md#required-components).

## Variables

| Variable | Query parameter | XML tag | Description |
| --- | --- | --- | --- |
| Activity Map object ID | `oid` | — | Object identifier for the last page ([`s_objectID`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/s-objectid)) in Activity Map. |
| Analytics for Target | `tnta` | `<a4t>` | Target data payload for Analytics for Target integrations. |
| Analytics visitor ID | `aid` | `<analyticsVisitorId>` | The Analytics visitor ID, stored in the `s_vi` cookie. Superseded by the Experience Cloud ID (`mid`) in modern implementations. |
| AppMeasurement flag | `ndh` | — | Added by AppMeasurement to every request to indicate that the hit originated from an AppMeasurement library. Do not alter. |
| Audience Manager blob | `aamb` | `<aamBlob>` | Encoded Audience Manager profile data passed during ID syncing through the Visitor ID Service. |
| Audience Manager location hint | `aamlh` | `<imsRegion>` | Integer representing the Audience Manager location hint, so data forwards to the correct Audience Manager regional data collection center. Retrieve it with the [getLocationHint](https://experienceleague.adobe.com/en/docs/id-service/using/id-service-api/methods/getlocationhint) function of the Visitor ID Service. Requires an ECID. |
| Browser height | `bh` | `<browserHeight>` | The [Browser height](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/browser-height) dimension. |
| Browser width | `bw` | `<browserWidth>` | The [Browser width](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/browser-width) dimension. |
| Campaign (tracking code) | `v0` | `<campaign>` | The [Tracking code](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/tracking-code) dimension. |
| Channel (site section) | `ch` | `<channel>` | The [Site section](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/site-section) dimension. |
| Character set | `ce` | — | The [`charSet`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/charset) implementation variable. |
| Client hints | `h.` (prefix) | `<userAgentClientHints>` | [Client hints](https://experienceleague.adobe.com/en/docs/analytics/technotes/client-hints). The query form prefixes each field with `h.`; the XML form nests child fields. See [Client hint fields](#client-hint-fields) below. |
| Color depth | `c` | `<colorDepth>` | The [Color depth](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/color-depth) dimension. |
| Connection type | `ct` | `<connectionType>` | The [Connection type](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/connection-type) dimension. |
| Context data | `c.[key]` | `<contextData.key>` | [`contextData`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/contextdata) implementation variables. When using query strings, context data keys are bracketed by `c.` (start) and `.c` (end) markers, which never contain a value. |
| Cookie lifetime | `cl` | — | The [`cookieLifetime`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/cookielifetime) implementation variable. |
| Cookie support | `k` | `<cookiesEnabled>` | The [Cookie support](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/cookie-support) dimension. |
| Currency code | `cc` | `<currencyCode>` | The [`currencyCode`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/currencycode) implementation variable. |
| Customer perspective | `cp` | `<customerPerspective>` | The [Hit type](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/hit-type) dimension. See [Context-aware sessions](https://experienceleague.adobe.com/en/docs/analytics/components/virtual-report-suites/vrs-mobile-visit-processing). |
| Dynamic variable prefix | `D` | — | The [`dynamicVariablePrefix`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/dynamicvariableprefix) implementation variable. |
| eVars | `v1` - `v250` | `<eVar1>` - `<eVar250>` | [eVar](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/evar) dimensions. |
| ECID | `mid` | `<marketingCloudVisitorId>` | The unique identifier used with the [Adobe Visitor ID Service](https://experienceleague.adobe.com/en/docs/id-service/using/home). Used in the [Experience Cloud Visitor ID](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/experience-cloud-visitor-id) dimension. See [Visitor identification using the Data Insertion API](https://experienceleague.adobe.com/en/docs/analytics/implementation/id/data-insertion). |
| Events | `events` | `<events>` | A comma-separated list of all numeric events for the hit. Most [Metrics](https://experienceleague.adobe.com/en/docs/analytics/components/metrics/overview) derive their data from this variable. The shorthand query parameter `ev` is also valid. |
| Fallback visitor ID | `fid` | `<fallbackVisitorId>` | The `s_fid` fallback [cookie](https://experienceleague.adobe.com/en/docs/core-services/interface/data-collection/cookies/analytics). |
| Generated timestamp | `t` | — | The auto-generated date/time of the hit, in the format `dd/mm/yyyy hh:mm:ss w o`:\<br/\>• `dd/mm/yyyy hh:mm:ss` — date/time in JavaScript (month `0` is January, `11` is December)\<br/\>• `w` — day of the week (`0` Sunday, `6` Saturday)\<br/\>• `o` — negative GMT offset in minutes (for example, `420` is GMT-7)\<br/\>For a custom timestamp, use the Timestamp variable instead. |
| IMS organization | `mcorgid` | `<marketingCloudOrgId>` | The Adobe IMS Org, which identifies the organization to the Visitor ID Service. |
| IP address | (HTTP header) | `<ipAddress>` | The visitor's IP address. When using query strings, the IP address comes from the request connection or the `X-Forwarded-For` header rather than a parameter. |
| Java enabled | `v` | `<javaEnabled>` | The [Java enabled](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/java-enabled) dimension. |
| Language | (HTTP header) | `<language>` | The [Language](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/language) dimension. When using query strings, language comes from the `Accept-Language` request header. |
| Last request timing | `lrt` | — | The roundtrip time for the last request, in milliseconds. Sent by AppMeasurement only when more than one request is sent from a single page, such as a single-page application (SPA). |
| Link name | `pev2` | `<linkName>` | The [Download link](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/download-link), [Exit link](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/exit-link), or [Custom link](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/custom-link) dimension, depending on the link type. If set, the page name is ignored. |
| Link type | `pe` | `<linkType>` | The type of link. Defaults to a custom link when a link name is present.\<br/\>Query values:\<br/\>• `lnk_d`: Download link\<br/\>• `lnk_e`: Exit link\<br/\>• `lnk_o`: Custom link\<br/\>• `tnt`: Analytics for Target\<br/\>XML values:\<br/\>• `d`: Download link\<br/\>• `e`: Exit link\<br/\>• `o`: Custom link |
| Link URL | `pev1` | `<linkUrl>` | The [`linkURL`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/linkurl) implementation variable. |
| List variables | `l1` - `l3` | `<list1>` - `<list3>` | [List variables](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/list). |
| Media audio flag | `ms_a` | — | Set by the Media SDK JS 3.x to `1` when the tracked streaming media is audio rather than video. |
| Page name | `pageName` | `<pageName>` | The [Page](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/page) dimension. The shorthand query parameter `gn` is also valid. |
| Page type | `pageType` | `<pageType>` | The [`pageType`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/pagetype) implementation variable. Set to `"errorPage"` on error pages, such as a 404 or 503. The shorthand query parameter `gt` is also valid. |
| Page URL | `g` | `<pageUrl>` | The [Page URL](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/page-url) dimension, up to 255 bytes. |
| Page URL overflow | `-g` | — | AppMeasurement automatically splits the URL into this parameter when its total length is longer than 255 bytes. |
| Persistent cookie check redirect | `pccr` | — | Set by data collection servers for new visitors to confirm the persistent cookie check occurred. Prevents infinite redirects if the visitor rejects cookies. |
| Platform flag | `pf` | — | Platform flag; for Adobe use only. Do not alter. |
| Products | `products` | `<products>` | The [`products`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/products) implementation variable. Used in the [Product](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/product) and [Category](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/category) dimensions. The shorthand query parameter `pl` is also valid. |
| Props | `c1` - `c75` | `<prop1>` - `<prop75>` | [Prop](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/prop) dimensions. |
| Purchase ID | `purchaseID` | `<purchaseId>` | The [`purchaseID`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/purchaseid) implementation variable. The shorthand query parameter `pi` is also valid. |
| Query string start / end | `AQB` / `AQE` | — | Delimits the query-string payload. `AQB=1` marks the start; `AQE=1` marks the end, confirming that the request was not truncated. |
| Referrer | `r` | `<referrer>` | The [`referrer`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/referrer) implementation variable. Used in traffic sources dimensions. |
| Report suite ID | (endpoint path) | `<reportSuiteId>` | The report suite(s) that receive the data. Separate multiple report suites with commas. XML encoding accepts the RSID in either the endpoint path or the body. |
| Resolution | `s` | `<resolution>` | The [Monitor resolution](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/monitor-resolution) dimension. |
| Server | `server` | `<server>` | The [Server](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/server) dimension. The shorthand query parameter `sv` is also valid. |
| Supplemental Data ID | `sdid` | — | Links multiple hits that describe the same event, such as Analytics and Target hits in an [Analytics for Target](https://experienceleague.adobe.com/en/docs/target/using/integrate/a4t/a4t) integration. |
| Target payload | `tnt` | — | Target data payload used in Target integrations. Sent when `pe=tnt`. |
| Time zone | (part of `t`) | `<timezone>` | The visitor's time zone offset from GMT, in hours (for example, `-8`). When using query strings, the time zone is carried inside the `t` parameter (the Generated timestamp) rather than as a separate parameter. |
| Timestamp | `ts` | `<timestamp>` | The [`timestamp`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/timestamp) implementation variable (timestamp override). See [Timestamps optional](https://experienceleague.adobe.com/en/docs/analytics/technotes/timestamps-optional). |
| Transaction ID | `xact` | `<transactionId>` | The [`transactionID`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/page-vars/transactionid) implementation variable. Ties online and offline data together with Data Sources. |
| Visitor ID override | `vid` | `<visitorId>` | The [`visitorID`](https://experienceleague.adobe.com/en/docs/analytics/implementation/vars/config-vars/visitorid) implementation variable, used to override the visitor ID. |
| Visitor ID new (server-set) | `vidn` | — | Set by data collection servers for new visitors, accompanying `pccr`. Contains the visitor ID value stored in the visitor cookie. |
| XML request version | — | `<scXmlVer>` | The Analytics XML request version number (for example, `1.0`). XML encoding only. |
| Zip code | `zip` | `<zip>` | The [Zip code](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/zip-code) dimension. |

## Client hint fields

[Client hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints) can be sent in either encoding. In the query string, each field uses an `h.` prefix (for example, `h.architecture`). In XML, the `<userAgentClientHints>` element is the parent node for the following child elements:

| Query parameter | XML tag | Data type |
| --- | --- | --- |
| `h.architecture` | `<architecture>` | String |
| `h.bitness` | `<bitness>` | String |
| `h.brands` | `<brands>` | Can contain one or more `<brand>` records — for example, `<brand><name>Chromium</name><version>100</version></brand>`. |
| `h.mobile` | `<mobile>` | Boolean |
| `h.model` | `<model>` | String |
| `h.platform` | `<platform>` | String |
| `h.platformVersion` | `<platformVersion>` | String |
| `h.wow64` | `<wow64>` | Boolean |

The following is an example `<userAgentClientHints>` XML object:

```xml
<userAgentClientHints>
    <architecture>x64</architecture>
    <bitness>64</bitness>
    <brands>
        <brand>
            <name>Chromium</name>
            <version>96</version>
        </brand>
    </brands>
    <mobile>false</mobile>
    <platform>Windows</platform>
    <platformVersion>NT 10.0</platformVersion>
    <wow64>true</wow64>
</userAgentClientHints>
```

See [User-agent client hints](https://experienceleague.adobe.com/en/docs/experience-platform/edge/fundamentals/user-agent-client-hints) in the Adobe Experience Platform Web SDK documentation for more information.

## Retired variables

These variables are retained only for legacy compatibility and should not be used in new implementations.

| Variable | Query parameter | XML tag | Description |
| --- | --- | --- | --- |
| Activity Map account marker | `u` | — | Account marker used in previous versions of Activity Map. |
| Activity Map object instance | `oi` | — | Object instance for the last page, used in previous versions of Activity Map. |
| Activity Map object tag | `ot` | — | Object name (tag) for the last page, used in previous versions of Activity Map. |
| Activity Map object type | `oidt` | — | Object identifier type for the last page, used in previous versions of Activity Map. |
| Activity Map page ID | `pid` | — | Page identifier for the last page, used in previous versions of Activity Map. |
| Activity Map page ID type | `pidt` | — | Page identifier type for the last page, used in previous versions of Activity Map. |
| Cookie domain periods | `cdp` | — | The `cookieDomainPeriods` implementation variable. |
| Hierarchy | `h1` - `h5` | `<hier1>` - `<hier5>` | Hierarchy dimensions. |
| Homepage flag | `hp` | `<homePage>` | Determined whether the current URL was the browser's homepage. |
| JavaScript version | `j` | `<javaScriptVersion>` | The JavaScript version installed in the browser. |
| Latitude | `lat` | `<latitude>` | Latitude, set by legacy mobile SDK implementations. |
| Longitude | `lon` | `<longitude>` | Longitude, set by legacy mobile SDK implementations. |
| Plug-ins | `p` | `<plugins>` | List of browser plug-in names. |
| State (US) | `state` | `<state>` | Captured the U.S. state a visitor entered, typically through a shipping or billing form. |
| Variable provider | `vvp` | — | Variable provider used in Data Connectors. |
| Video milestones | `pev3` | — | Tracked milestones in previous versions of video reporting. |
| Visitor migration key | `vmt` | — | Helped migrate implementations from third-party to first-party cookies. |
| Visitor migration server | `vmf` | — | Used during migration from third-party to first-party cookies. |
| Visitor namespace | `ns` | — | Helped determine where cookies are set. |
