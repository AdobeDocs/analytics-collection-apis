---
title: Media Collection API request parameters
description: Reference for the parameters accepted by Adobe Analytics Media Collection API requests.
---

# Media Collection API request parameters

This reference lists the parameters accepted by Streaming Media Collection API requests, grouped by category, and explains how request bodies are validated. Parameters are sent inside the `params` object of a request body (quality-of-experience parameters are sent in `qoeData`).

In the tables below, **Required** indicates whether the parameter is mandatory for the event it is set on, **Type** is the JSON data type, and **Set on** is the event type the parameter is sent with.

## Analytics data

| Request key | Required | Type | Set on | Description |
| --- | :---: | :---: | :---: | --- |
| `analytics.trackingServer` | Y | string | `sessionStart` | The URL of your Adobe Analytics server. |
| `analytics.reportSuite` | Y | string | `sessionStart` | The ID that identifies your Analytics reporting data. |
| `analytics.enableSSL` | N | boolean | `sessionStart` | `true` or `false` to send Analytics data over HTTPS. |
| `analytics.visitorId` | N | string | `sessionStart` | A custom Adobe visitor ID you can use across multiple Adobe applications. Equivalent to the Analytics visitor ID (VID). |
| `analytics.aid` | N | string | `sessionStart` | The Analytics legacy user ID (aid). See [Legacy and declared user IDs](#legacy-and-declared-user-ids). |

## Visitor data

| Request key | Required | Type | Set on | Description |
| --- | :---: | :---: | :---: | --- |
| `visitor.marketingCloudOrgId` | Y | string | `sessionStart` | The Experience Cloud organization ID; identifies your organization within Adobe Experience Cloud. |
| `visitor.marketingCloudUserId` | N | string | `sessionStart` | The Experience Cloud ID (ECID). In most scenarios this is the ID you should use to identify a user. Equivalent to the `MID` in Adobe Analytics. While not technically required, this parameter is necessary for accessing Experience Cloud apps and services. |
| `visitor.aamLocationHint` | N | integer | `sessionStart` | Provides the Adobe Audience Manager Edge region. If no value is entered, the value is null. See [visitor.aamLocationHint](#visitoraamlocationhint). |
| `visitor.customerIDs` | N | object | `sessionStart` | Declared customer IDs. See [Legacy and declared user IDs](#legacy-and-declared-user-ids). |
| `appInstallationId` | N | string | `sessionStart` | Uniquely identifies the app and the device. See [appInstallationId](#appinstallationid). |

## Content data

| Request key | Required | Type | Set on | Description |
| --- | :---: | :---: | :---: | --- |
| `media.id` | Y | string | `sessionStart` | Unique identifier for the content. |
| `media.name` | N | string | `sessionStart` | Human-readable name for the content. |
| `media.length` | Y | number | `sessionStart` | Content length, in seconds. |
| `media.contentType` | Y | string | `sessionStart` | Format of the stream. Can be any string; recommended values are `Live`, `VOD`, or `Linear`. |
| `media.streamType` | N | string | `sessionStart` | The type of stream, for example `video` or `audio`. |
| `media.playerName` | Y | string | `sessionStart` | The name of the player responsible for rendering the content. |
| `media.channel` | Y | string | `sessionStart` | The channel of distribution — for example a mobile application name, website name, or property name. |
| `media.publisher` | N | string | `sessionStart` | The content publisher or distributor. |
| `media.resume` | N | boolean | `sessionStart` | Indicates whether the user is resuming a previous session rather than starting a new one. See [media.resume](#mediaresume). |
| `media.sdkVersion` | N | string | `sessionStart` | The SDK version used by the player. |
| `media.libraryVersion` | N | string | `sessionStart` | The version of the media library sending the data. |

## Content standard metadata

| Request key | Required | Type | Set on | Description |
| --- | :---: | :---: | :---: | --- |
| `media.streamFormat` | N | string | `sessionStart` | Stream format, for example `HD`. |
| `media.show` | N | string | `sessionStart` | The program or series name. |
| `media.season` | N | string | `sessionStart` | The season number the show or series belongs to. |
| `media.episode` | N | string | `sessionStart` | The number of the episode. |
| `media.assetId` | N | string | `sessionStart` | The unique identifier for the video asset content — such as the TV series episode identifier, movie asset identifier, or live event identifier. These IDs are typically derived from metadata authorities such as EIDR, TMS/Gracenote, or Rovi, but can also come from other proprietary or in-house systems. |
| `media.genre` | N | string | `sessionStart` | The type of content, as defined by the content producer. |
| `media.firstAirDate` | N | string | `sessionStart` | The date the content first aired on television. |
| `media.firstDigitalDate` | N | string | `sessionStart` | The date the content first aired on any digital platform. |
| `media.rating` | N | string | `sessionStart` | The rating, as defined by TV Parental Guidelines. |
| `media.originator` | N | string | `sessionStart` | The creator of the content. |
| `media.network` | N | string | `sessionStart` | The network or channel name. |
| `media.showType` | N | string | `sessionStart` | The type of content, expressed as an integer between 0 and 3:\<br/\>• `0` — Full episode\<br/\>• `1` — Preview\<br/\>• `2` — Clip\<br/\>• `3` — Other |
| `media.adLoad` | N | string | `sessionStart` | The type of ad loaded. |
| `media.pass.mvpd` | N | string | `sessionStart` | The MVPD provided by Adobe Pass authentication. |
| `media.pass.auth` | N | string | `sessionStart` | Indicates the user has been authorized by Adobe Pass authentication. Can only be `true` if set. |
| `media.dayPart` | N | string | `sessionStart` | The time of day when the content was broadcast. |
| `media.feed` | N | string | `sessionStart` | The type of feed, for example `West-HD`. |

## Ad data

| Request key | Required | Type | Set on | Description |
| --- | :---: | :---: | :---: | --- |
| `media.ad.podFriendlyName` | N | string | `adBreakStart` | Friendly name of the ad break. |
| `media.ad.podIndex` | Y | integer | `adBreakStart` | The index of the ad pod in the video. |
| `media.ad.podSecond` | Y | number | `adBreakStart` | The second at which the pod started. |
| `media.ad.podPosition` | Y | integer | `adStart` | The index of the ad inside the ad break, starting at 1. |
| `media.ad.name` | N | string | `adStart` | Friendly name of the ad. |
| `media.ad.id` | Y | string | `adStart` | Name (ID) of the ad. |
| `media.ad.length` | Y | number | `adStart` | Length of the video ad, in seconds. |
| `media.ad.playerName` | Y | string | `adStart` | The name of the player responsible for rendering the ad. |

## Ad standard metadata

| Request key | Required | Type | Set on | Description |
| --- | :---: | :---: | :---: | --- |
| `media.ad.advertiser` | N | string | `adStart` | The company or brand whose product is featured in the ad. |
| `media.ad.campaignId` | N | string | `adStart` | The ID of the ad campaign. |
| `media.ad.creativeId` | N | string | `adStart` | The ID of the ad creative. |
| `media.ad.siteId` | N | string | `adStart` | The ID of the ad site. |
| `media.ad.creativeURL` | N | string | `adStart` | The URL of the ad creative. |
| `media.ad.placementId` | N | string | `adStart` | The placement ID of the ad. |

## Chapter data

| Request key | Required | Type | Set on | Description |
| --- | :---: | :---: | :---: | --- |
| `media.chapter.index` | Y | integer | `chapterStart` | Identifies the chapter's position in the content. |
| `media.chapter.offset` | Y | number | `chapterStart` | The second in the playback where the chapter starts. |
| `media.chapter.length` | Y | number | `chapterStart` | The length of the chapter, in seconds. |
| `media.chapter.friendlyName` | N | string | `chapterStart` | The human-friendly name of the chapter. |

## Player state data

| Request key | Required | Type | Set on | Description |
| --- | :---: | :---: | :---: | --- |
| `media.state.name` | Y | string | `stateStart`, `stateEnd` | The name of the custom player state, such as full screen or mute. |

## Quality data

Quality-of-experience parameters are sent in the `qoeData` object rather than `params`.

| Request key | Required | Type | Set on | Description |
| --- | :---: | :---: | :---: | --- |
| `media.qoe.bitrate` | N | integer | Any | The average bitrate, in bits per second. Computed as a weighted average of all bitrate values, related to the play duration that occurred during a playback session. |
| `media.qoe.droppedFrames` | N | integer | Any | The number of dropped frames in the stream. |
| `media.qoe.framesPerSecond` | N | integer | Any | The number of frames per second. |
| `media.qoe.timeToStart` | N | integer | Any | The time, in milliseconds, between when the user hits play and the content loads and starts playing. |
| `media.qoe.errorID` | N | string | Any | An identifier for a player error. |
| `media.qoe.errorSource` | N | string | Any | The source of a player error, for example `player`. |

## California Consumer Privacy Act (CCPA) parameters

| Request key | Required | Type | Set on | Description |
| --- | :---: | :---: | :---: | --- |
| `analytics.optOutServerSideForwarding` | N | boolean | `sessionStart` | Set to `true` when the end user has opted out of their data being shared between Adobe Analytics and other Experience Cloud solutions, such as Audience Manager. |
| `analytics.optOutShare` | N | boolean | `sessionStart` | Set to `true` when the end user has opted out of their data being federated, for example to other Adobe Analytics clients. |

## Additional details

### visitor.marketingCloudUserId

Pass the Experience Cloud ID (also known as the `MID` or `MCID`) on the `sessionStart` call by including it in the `params` map using the key `visitor.marketingCloudUserId`. This is useful if you already integrate with other Experience Cloud products and have already obtained the ECID.

<InlineAlert variant="note" slots="text"/>

The Media Collection API integrates with the Experience Cloud family of apps (Adobe Analytics, Audience Manager, Target, and so on). You need an Experience Cloud ID to access these apps. The ECID is what you should use to identify users in most scenarios.

### appInstallationId

* **If you do not pass an `appInstallationId` value** — The back end no longer generates an ECID and instead relies on Adobe Analytics to do so. Adobe recommends sending either an ECID if available, or an `appInstallationId` (along with the still-mandatory `visitor.marketingCloudOrgId`) so that the Media Collection API generates the ECID and sends it on all calls.
* **If you do pass an `appInstallationId` value** — The ECID can be generated by the back end when you pass values for both `appInstallationId` and the required `visitor.marketingCloudOrgId`. If you pass `appInstallationId` yourself, you must persist its value on the client side. It must be unique to the app on a device and must persist for as long as the app is not reinstalled.

<InlineAlert variant="note" slots="text"/>

The `appInstallationId` uniquely identifies the app and the device. It must be unique for each app on each device: two users running the same version of the same app on different devices must each send a different, unique `appInstallationId`.

### visitor.marketingCloudOrgId

In addition to being necessary for ECID generation when one is not provided, this parameter is also used as the publisher ID, which the Media Collection API uses for [federation rule matching](https://experienceleague.adobe.com/en/docs/media-analytics/using/media-use-cases/federated-media).

### Legacy and declared user IDs

* **`analytics.aid`** — The value must be a string representing the Analytics legacy user ID.
* **`visitor.customerIDs`** — The value must be an object of the following format:

  ```js
  "<<insert your ID name here>>": {
    "id": "<<insert your id here>>",
    "authState": <<insert one of 0, 1, 2>>
  }
  ```

  The `visitor.customerIDs` value can contain any number of objects in this format.

### visitor.aamLocationHint

This parameter indicates which Adobe Audience Manager (AAM) Edge is used when Adobe Analytics sends the customer data to Audience Manager. If no value is entered, the value is null. This is particularly important when end users tend to use their devices in geographically distant locations (for example US-East, US-West, Europe, Asia). Otherwise, user data is spread across multiple AAM Edges.

### media.resume

If the app determines that a session was closed and then resumed later — for example, the user left the video but eventually came back and the player resumed from the playhead where it stopped — send an optional boolean `media.resume` parameter inside the `params` object of the `sessionStart` call.

## Request validation

The Media Collection API back end validates each request body against a JSON schema specific to its event type. When validation fails, the response returns a `400` with an error message. These schemas are the authority for which parameters are required or optional for each event, and their data types.

The schemas are publicly accessible:

`GET https://{uri}/api/v1/schemas/{event-type}`

For example, `GET https://{uri}/api/v1/schemas/sessionStart` returns the schema for the `sessionStart` event. Each schema is a standard JSON Schema (draft-04) document. The following excerpt shows its shape:

```json
{
  "$schema": "https://json-schema.org/draft-04/schema#",
  "definitions": {
    "playerTime": {
      "type": "object",
      "properties": {
        "playhead": { "type": "number" },
        "ts": { "type": "integer" }
      },
      "required": ["playhead", "ts"],
      "additionalProperties": false
    },
    "eventType": {
      "type": "string",
      "enum": ["sessionStart", "play", "ping", "..."]
    }
  },
  "type": "object",
  "$ref": "#/definitions/sessionStart"
}
```

<InlineAlert variant="note" slots="text"/>

Session-level validation is not possible, because the session context is not available in the collection layer. Each event is validated independently.

For a practical walkthrough of validating requests during implementation, see [Validating event requests](implementation.md#validating-event-requests).
