---
title: Events endpoint
description: Send playback events to an active session using the Adobe Analytics Media Collection API events endpoint.
---

# Events endpoint

The events endpoint sends playback events to an active session in the Streaming Media Collection API (such as pings, pauses, chapters, and ad events). This page documents the request URI, request body, event types, response, and response codes.

## Request

`POST https://{uri}/api/v1/sessions/{sid}/events`

Send an HTTP `POST` with a JSON request body and the `Content-Type: application/json` header. For the complete machine-readable definition, see the [Media Collection API reference](../../api/media-collection.md).

### URI parameter

`sid`: The session ID returned in the `Location` header of a [sessions request](sessions.md).

### Request body

The request body must be JSON and has the same structure as a sessions request. The following example is a `play` event.

```json
{
    "playerTime": {
        "playhead": 30,
        "ts": 1731672030000
    },
    "eventType": "play",
    "params": {}
}
```

Field notes:

* `playerTime` (required)
    * `playhead`: The playhead position, in seconds. The value can be a floating-point number.
    * `ts`: The timestamp, in milliseconds.
* `eventType` (required): See [Event types](#event-types).
* `params` (optional; required for some event types): See [Request parameters](parameters.md).
* `customMetadata` (optional): Sent only with the `sessionStart`, `adStart`, and `chapterStart` event types. See [Custom metadata](custom-metadata.md).
* `qoeData` (optional): Quality-of-experience data.

## Event types

The `eventType` member identifies the media event. The following values are sent on the wire:

| Event type | Description |
|------------|-------------|
| `sessionStart` | Starts the session. Sent to the [sessions endpoint](sessions.md), not this endpoint. |
| `play` | Playback started or resumed. |
| `ping` | A heartbeat call, sent on a regular interval during playback. See [Sending ping events](implementation.md#sending-ping-events). |
| `pauseStart` | Playback paused. |
| `bufferStart` | Buffering started. |
| `bitrateChange` | The streaming bitrate changed. |
| `error` | A player error occurred. |
| `sessionComplete` | The main content finished playing. |
| `sessionEnd` | The session ended before the content finished, for example when the user abandons playback. |
| `adBreakStart` | An ad break (pod) started. |
| `adBreakComplete` | An ad break finished. |
| `adStart` | An individual ad started. |
| `adComplete` | An individual ad finished. |
| `adSkip` | An ad was skipped. |
| `chapterStart` | A chapter or segment started. |
| `chapterComplete` | A chapter finished. |
| `chapterSkip` | A chapter was skipped. |
| `stateStart` | A custom player state started, such as full screen or mute. |
| `stateEnd` | A custom player state ended. |

Note the wire spelling: use `pauseStart` (not `pause`), and `ping` for the heartbeat. Resuming after a pause, buffer, or seek is reported with a `play` event; there are no separate `pause`, `bufferComplete`, `seekStart`, or `seekComplete` event types on the wire.

<InlineAlert variant="warning" slots="text"/>

You can only track ads inside an ad break. Without the `adBreakStart` and `adBreakComplete` events bookending your ads, `adStart` and `adComplete` events are ignored and the ad duration is counted as main content. This can significantly skew the aggregated data in Adobe Analytics.

## Response

A successful call returns `204 No Content` with no response body. Client implementations typically treat event calls as fire-and-forget and do not inspect the response beyond the status code.

```http
HTTP/1.1 204 No Content
Date: Thu, 15 Jan 2026 19:15:24 GMT
Connection: keep-alive
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: OPTIONS,POST,PUT
Access-Control-Allow-Headers: Content-Type
Access-Control-Expose-Headers: Location
```

## Response codes

| HTTP response code | Description | Client action |
|--------------------|-------------|---------------|
| `204` | No content. The event was accepted successfully. | None. |
| `400` | Bad request. The request had an improper format. | Check the [validation schema](parameters.md#request-validation) for the event type. |
| `404` | Not found. The session ID was not found in the back-end service. | Start a new session with the [sessions endpoint](sessions.md) and report tracking on it. |
| `410` | Gone. The session was found but can no longer accept activity. | Start a new session with the [sessions endpoint](sessions.md) and report tracking on it. |
| `500` | Server error. | None. |
