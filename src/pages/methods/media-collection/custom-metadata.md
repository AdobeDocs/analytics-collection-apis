---
title: Media Collection API custom metadata
description: Send custom key-value pairs on the sessionStart, adStart, and chapterStart events of the Media Collection API.
---

# Media Collection API custom metadata

The Streaming Media Collection API lets you send custom key-value pairs alongside the standard parameters on `sessionStart`, `adStart`, and `chapterStart` events. Custom metadata is forwarded to Adobe Analytics with the corresponding media close events.

To make this data available in Analysis Workspace, define custom eVars and configure processing rules to populate them for your use case. Once mapped to eVars or props, the data also becomes available in Adobe Experience Platform through the corresponding eVar paths, provided the [Analytics source connector](https://experienceleague.adobe.com/en/docs/experience-platform/sources/connectors/adobe-applications/analytics) is configured.

For XDM-based implementations that use the Adobe Experience Platform Edge Network, see the [Media Edge API](https://developer.adobe.com/data-collection-apis/docs/api/media-edge).

## Overview

Custom metadata is included in the request body as a `customMetadata` object, positioned alongside the `params` key. It applies to three event types:

| Event | Metadata applies to |
|-------|---------------------|
| `sessionStart` | Main content (entire session) |
| `adStart` | Individual advertisement |
| `chapterStart` | Content chapter or segment |

## Structure

Custom metadata is a flat object of key-value pairs at the event level, alongside the `params` key:

```json
{
  "playerTime": {
    "playhead": 0,
    "ts": 1646938800000
  },
  "eventType": "sessionStart",
  "params": {
    "analytics.trackingServer": "example.data.adobedc.net",
    "analytics.reportSuite": "example-rsid",
    "visitor.marketingCloudOrgId": "0123456789@AdobeOrg",
    "media.id": "sample-video-id",
    "media.length": 3600,
    "media.contentType": "vod",
    "media.playerName": "HTML5 Player",
    "media.channel": "Sports"
  },
  "customMetadata": {
    "field": "value"
  }
}
```

### Mandatory parameters by event type

| Event | Required `params` |
|-------|-------------------|
| `sessionStart` | `analytics.trackingServer`, `analytics.reportSuite`, `visitor.marketingCloudOrgId`, `media.id`, `media.length`, `media.contentType`, `media.playerName`, `media.channel` |
| `adStart` | `media.ad.id`, `media.ad.length`, `media.ad.podPosition`, `media.ad.playerName` |
| `chapterStart` | `media.chapter.length`, `media.chapter.offset`, `media.chapter.index` |

### Key naming requirements

* Avoid the `media.` prefix in custom metadata keys. It maps to standard media fields and may overwrite them in Analytics reporting.
* The `a.` prefix is reserved for Adobe standard metadata and must not be used.

## Main content custom metadata

Sent with `sessionStart`. Applies to the primary media being tracked and remains available throughout ad and chapter calls. Any custom metadata defined here is automatically merged by the back end on the corresponding close calls, alongside any custom metadata defined for ads and chapters.

```sh
curl -X POST "https://{uri}/api/v1/sessions" \
--header 'Content-Type: application/json' \
--data '{
  "playerTime": {
    "playhead": 0,
    "ts": 1646938800000
  },
  "eventType": "sessionStart",
  "params": {
    "analytics.trackingServer": "example.data.adobedc.net",
    "analytics.reportSuite": "example-rsid",
    "analytics.visitorId": "visitor123",
    "visitor.marketingCloudOrgId": "0123456789@AdobeOrg",
    "media.id": "sample-video-id",
    "media.name": "Sample Video",
    "media.length": 3600,
    "media.contentType": "vod",
    "media.playerName": "HTML5 Player",
    "media.channel": "Sports"
  },
  "customMetadata": {
    "contentCategory": "Live Sports",
    "leagueType": "Professional",
    "broadcastRights": "Premium"
  }
}'
```

## Ad custom metadata

Sent with `adStart`. Specific to each individual advertisement. The custom metadata from `sessionStart` is also automatically merged by the back end on the ad close call, alongside any ad-specific custom metadata defined here.

```sh
curl -X POST "https://{uri}/api/v1/sessions/{sid}/events" \
--header 'Content-Type: application/json' \
--data '{
  "playerTime": {
    "playhead": 30,
    "ts": 1646938830000
  },
  "eventType": "adStart",
  "params": {
    "media.ad.id": "summer-sale-2026",
    "media.ad.name": "Summer Sale Ad",
    "media.ad.length": 30,
    "media.ad.playerName": "HTML5 Player",
    "media.ad.podPosition": 1
  },
  "customMetadata": {
    "campaignId": "SUMMER2026",
    "targetAudience": "18-34",
    "adFormat": "skippable"
  }
}'
```

## Chapter custom metadata

Sent with `chapterStart`. Specific to each content chapter or segment. The custom metadata from `sessionStart` is also automatically merged by the back end on the chapter close call, alongside any chapter-specific custom metadata defined here.

```sh
curl -X POST "https://{uri}/api/v1/sessions/{sid}/events" \
--header 'Content-Type: application/json' \
--data '{
  "playerTime": {
    "playhead": 600,
    "ts": 1646938200000
  },
  "eventType": "chapterStart",
  "params": {
    "media.chapter.friendlyName": "Introduction",
    "media.chapter.length": 300,
    "media.chapter.index": 1,
    "media.chapter.offset": 600
  },
  "customMetadata": {
    "chapterType": "tutorial",
    "difficulty": "beginner",
    "instructor": "Jane Smith"
  }
}'
```

## Behavior

* All custom metadata values must be strings. Convert numbers and booleans before sending.
* Custom metadata appears in Analytics with a `c.` prefix. For example, `contentCategory` becomes `c.contentCategory`.
* Map custom metadata to eVars, props, or context data variables through Analytics processing rules.
* `sessionStart` metadata persists for the entire session; updating it requires a new session.
* Each `adStart` and `chapterStart` event can carry different custom metadata.

## Related content

* [Request parameters](parameters.md)
* [Events endpoint](events.md)
* [Media Edge API](https://developer.adobe.com/data-collection-apis/docs/api/media-edge/)
* [Adobe Analytics source connector for report suite data](https://experienceleague.adobe.com/en/docs/experience-platform/sources/connectors/adobe-applications/analytics)
