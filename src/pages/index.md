---
title: Adobe Analytics data collection APIs
description: Methods for sending data directly to Adobe Analytics, including the Data Insertion, Bulk Data Insertion, and Media Collection APIs.
---

<Superhero slots="image, heading, text" background="rgb(6, 19, 76)"/>

![Hero image](assets/hero-illustration.png)

# Adobe Analytics data collection APIs

Send data directly to Adobe Analytics collection servers without a client-side library (AppMeasurement or tags).

<Resources slots="heading, links"/>

#### Resources

* [API reference](api/index.md)
* [Github repository](https://github.com/AdobeDocs/analytics-collection-apis)

## Overview

This documentation covers the methods for programmatically collecting data into Adobe Analytics, including:

* **[Data Insertion API](methods/data-insertion/index.md)**: Send event data one hit at a time using a query string or as XML.
* **[Bulk Data Insertion API](methods/bulk-data-insertion/index.md)**: Upload batches of server call data to Adobe Analytics.
* **[Media Collection API](methods/media-collection/index.md)**: Track streaming media sessions server-side with RESTful HTTP calls.

## Compare each method

Analytics collection methods differ by volume, timing, authentication, and payload. This table provides a high level comparison between each method.

| Method | Use it when | Volume and timing | Authentication | Payload and transport |
| --- | --- | --- | --- | --- |
| [Data Insertion API](methods/data-insertion/index.md) | Implementing Analytics server-side or independent of a data collection library | One hit per request, real time | None | A query string (`GET` or `POST`), or an XML `POST` |
| [Bulk Data Insertion API](methods/bulk-data-insertion/index.md) | You collect high volumes of data server-side, backfill historical data, or feed data from an ETL or offline pipeline | Batches of many events per file, up to one file per second; optimized for large files sent less frequently | [OAuth Server-to-Server](methods/bulk-data-insertion/authentication.md) | Gzip-compressed CSV files uploaded as `multipart/form-data` |
| [Media Collection API](methods/media-collection/index.md) | You track streaming audio or video server-side without the Media SDK | One media event per request, real time, grouped into a session | None | An HTTP `POST` with a JSON body |

## Other data collection methods

* **Data collection libraries**: Includes AppMeasurement, the Web SDK, and their respective tag extensions. These libraries generate image requests for you using client-side variables. See the [Adobe Analytics implementation guide](https://experienceleague.adobe.com/en/docs/analytics/implementation/home).
* **Edge Network APIs**: For Edge Network collection, including the Media Edge API, see the [Data Collection APIs](https://developer.adobe.com/data-collection-apis/docs/).
* **Data Sources**: To import offline or external data on a scheduled basis and tie it to existing Analytics data, see [Data Sources](https://experienceleague.adobe.com/en/docs/analytics/import/data-sources/overview) in the Analytics Import guide.

This user guide adheres to Adobe's Code of Conduct. Contributions are encouraged and appreciated. See Adobe's [Code of Conduct](https://github.com/AdobeDocs/analytics-collection-apis/blob/main/CODE_OF_CONDUCT.md) and [Contribution Guidelines](https://github.com/AdobeDocs/analytics-collection-apis/blob/main/.github/CONTRIBUTING.md) on GitHub for more information.
