---
title: Sessions endpoint
description: Start a media tracking session using the Adobe Analytics Media Collection API sessions endpoint.
---

# Sessions endpoint

The sessions endpoint starts a new media tracking session in the Streaming Media Collection API and returns a session ID used for all subsequent event calls. This page documents the request URI, request body, and response codes.

## Request

`POST https://{uri}/api/v1/sessions`

Send an HTTP `POST` with a JSON request body and the `Content-Type: application/json` header. Obtain your `{uri}` endpoint from your Adobe representative. For the complete machine-readable definition, see the [Media Collection API reference](../../api/media-collection.md).

### URI parameters

None.

### Request body

The request body must be JSON and must have the same structure as the following `sessionStart` example. The `params` object carries the Analytics, visitor, and media parameters that identify and describe the session. See [Request parameters](parameters.md) for the full list and [request validation](parameters.md#request-validation) for which parameters are required.

<CodeBlock slots="heading, code" repeat="2" languages="JSON,HTTP"/>

#### Request body

```json
{
    "playerTime": {
        "playhead": 0,
        "ts": 1731672000000
    },
    "eventType": "sessionStart",
    "params": {
        "analytics.trackingServer": "example.data.adobedc.net",
        "analytics.reportSuite": "example-rsid",
        "analytics.enableSSL": true,
        "analytics.visitorId": "<your-visitor-id>",
        "visitor.marketingCloudOrgId": "0123456789ABCDEF0123456789@AdobeOrg",
        "media.id": "sample-video-id",
        "media.name": "Sample video",
        "media.length": 3600,
        "media.contentType": "vod",
        "media.playerName": "sample-html5-player",
        "media.channel": "sample-channel"
    }
}
```

#### Response

```http
HTTP/1.1 201 Created
Date: Wed, 14 Jan 2026 19:14:51 GMT
Content-Type: application/octet-stream
Content-Length: 0
Location: /api/v1/sessions/a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: OPTIONS,POST,PUT
Access-Control-Allow-Headers: Content-Type
Access-Control-Expose-Headers: Location
```

Field notes:

* `playerTime` (required)
    * `playhead`: The playhead position, in seconds. For live content, use the current second of the day, where `0 <= playhead < 86400`. For recorded content, use the current second of content, where `0 <= playhead < content length`. The value can be a floating-point number.
    * `ts`: The timestamp, in milliseconds, in Coordinated Universal Time (UTC).
* `eventType` (required): For a sessions request this is always `sessionStart`.
* `params` (required): See [Request parameters](parameters.md).
* `customMetadata` (optional): Custom key-value pairs. See [Custom metadata](custom-metadata.md).
* `qoeData` (optional): Quality-of-experience data.

## Obtaining the session ID

On success the endpoint returns `201 Created` with an empty body. The session ID is returned in the **`Location`** response header, whose value is the relative path `/api/v1/sessions/{sid}`. The `/api/v1/` segment identifies the API version; the segment after `sessions/` is the session ID. Parse the session ID from that header and use it in every [events request](events.md) for the session. Only a `201` response indicates the session was created.

## Response codes

| HTTP response code | Description |
|--------------------|-------------|
| `201` | Session created. |
| `400` | Bad request. The request body did not pass validation. |
| `500` | Server error. |
