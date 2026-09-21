---
title: Implement the Media Collection API
description: Step-by-step guidance for implementing streaming media tracking with the Adobe Analytics Media Collection API.
---

# Implement the Media Collection API

This guide walks through implementing media tracking with the Streaming Media Collection API — from a quick start and obtaining a session ID through sending events, pings, and quality-of-experience data, plus timeout, ordering, and queuing considerations. To attach custom key-value pairs to your events, see [Custom metadata](custom-metadata.md).

## Quick start

<InlineAlert variant="info" slots="text"/>

Before you build a full implementation, verify your request data by sending requests manually with a tool such as `curl` or Postman. Manual requests give you immediate feedback on incorrect data types or values. Use the [validation schemas](parameters.md#request-validation) to confirm you are supplying valid request data.

To start tracking:

1. Gather the required Adobe Analytics and visitor data: the Experience Cloud organization ID, the Experience Cloud ID (ECID), the Analytics report suite ID, and the Analytics tracking server URL.
2. Build a JSON body for your sessions request with the minimum data required for a successful call.
3. Send the sessions request to your endpoint. If the payload is invalid, correct it and retry until you receive a `201 Created` response.

Example sessions request body:

```json
{
    "playerTime": {
        "playhead": 0,
        "ts": 1731672000000
    },
    "eventType": "sessionStart",
    "params": {
        "analytics.trackingServer": "[YOUR_TRACKING_SERVER]",
        "analytics.reportSuite": "[YOUR_RSID]",
        "analytics.enableSSL": true,
        "visitor.marketingCloudOrgId": "[YOUR_ORG_ID]",
        "visitor.marketingCloudUserId": "[YOUR_ECID]",
        "media.id": "sample-video-id",
        "media.name": "Sample video",
        "media.length": 3600,
        "media.contentType": "vod",
        "media.playerName": "sample-html5-player",
        "media.channel": "sample-channel"
    }
}
```

<InlineAlert variant="note" slots="text"/>

Use the correct data types in the JSON body. For example, `analytics.enableSSL` requires a boolean and `media.length` is numeric. Check the [validation schemas](parameters.md#request-validation) for parameter types and whether each parameter is required or optional.

Example request and response with `curl`, where the JSON body is in a file named `sample_data_session`:

```sh
curl -i -d @sample_data_session https://{uri}/api/v1/sessions

HTTP/1.1 201 Created
Date: Mon, 18 Jan 2026 22:34:12 GMT
Content-Type: application/octet-stream
Content-Length: 0
Connection: keep-alive
Location: /api/v1/sessions/a39c037641f2c...   # Session ID
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: OPTIONS,POST,PUT
Access-Control-Allow-Headers: Content-Type
Access-Control-Expose-Headers: Location
```

On success, the `201 Created` response includes the session ID in the `Location` header. The session ID is required for all subsequent tracking calls — see [Obtaining a session ID](#obtaining-a-session-id).

## Setting the HTTP request type

Every Media Collection API request body must be JSON, so set the content type in your player. For example, in JavaScript, set the `Content-Type` request header:

```js
httpRequest.setRequestHeader('Content-Type', 'application/json');
```

## Obtaining a session ID

When you send a [sessions request](sessions.md), the session ID is returned in the `Location` response header as the relative path `/api/v1/sessions/{sid}`. Parse the segment after `sessions/` and store it — you pass it in the URL of every events request. Only a `201` response indicates the session was created.

```js
// After a successful POST to /api/v1/sessions:
const location = response.headers.get("Location"); // e.g. "/api/v1/sessions/{sid}"
const sessionId = location.split("/").pop();
```

## Implementing an events request

After you obtain a session ID, use the [events endpoint](events.md) for all subsequent tracking calls:

`POST https://{uri}/api/v1/sessions/{sid}/events`

Specify the playhead and timestamp (`playerTime`), the `eventType`, and any optional parameters in the JSON body. The events body has the same structure as the sessions body. For the full list of event types and response codes, see [Events endpoint](events.md).

## Validating event requests

The back end validates each event's request body against a JSON schema for its event type. When validation fails, the response returns a `400` with an error message. The schemas are publicly accessible at `GET https://{uri}/api/v1/schemas/{event-type}` and are the authority for which parameters are required and their data types. For the schema shape and a `sessionStart` example, see [Request validation](parameters.md#request-validation).

## Sending ping events

Ping events are the heartbeat of the Media Collection API. Fire a `ping` event every 10 seconds during main content playback, beginning after the first 10 seconds of playback, regardless of any other events you send. During ad playback, the client Media SDKs ping every second for finer ad-tracking granularity (configurable back to 10 seconds). The only required members of a ping are `eventType: "ping"` and the `playerTime` object.

```js
// Fire a ping every 10 seconds during playback.
const pingTimer = setInterval(() => sendEvent("ping"), 10000);

// Stop pinging when playback ends.
clearInterval(pingTimer);
```

## Sending quality-of-experience data

Any event can carry an optional `qoeData` object alongside `params`. It reports playback quality — bitrate, dropped frames, frame rate, startup time, and error details. See the [quality data parameters](parameters.md#quality-data).

```json
{
  "playerTime": { "playhead": 30, "ts": 1731672030000 },
  "eventType": "bitrateChange",
  "params": {},
  "qoeData": {
    "media.qoe.bitrate": 2000000,
    "media.qoe.droppedFrames": 4,
    "media.qoe.framesPerSecond": 30,
    "media.qoe.timeToStart": 1200
  }
}
```

## Timeout conditions

The Media Collection API is stateless and, unlike the Media SDK, does not automatically issue a new session ID when a session times out. When a timeout occurs, the back end closes the session and drops all subsequent calls made with that session ID. Your client must monitor the timeout conditions and obtain a new session ID when one occurs. The back end closes a session under either of these conditions:

* **No API events for 10 minutes.** If the back end receives no API events, it closes the session.
* **No playhead change for 30 minutes.** If the playhead does not move for 30 minutes — for example, the user pauses and walks away — the back end closes the session.

In addition, the client Media SDKs restart long-running sessions every 24 hours. Start a new session if a session approaches this limit.

<InlineAlert variant="note" slots="text"/>

You can also force a session to end by sending an events request with the `sessionEnd` event type.

## Controlling the order of events

Streaming tracking is highly time-dependent, and calls occasionally arrive at the back end out of order. The back end buffers events in a window — five seconds or a maximum of 10 events — then reorders them by `playerTime.ts` before sending them to the processing pipeline. Reordering may fail if the delay between out-of-order calls exceeds one second.

For example, an `adBreakStart` immediately followed by an `adStart` can arrive out of order, because the two calls fire almost simultaneously. Always keep at least a 1-millisecond difference between the timestamps of consecutive events, so the back end can restore the correct order; two events must never share a timestamp.

<InlineAlert variant="note" slots="text"/>

The `sessionStart` event is an exception: it is sent to the processing pipeline immediately rather than buffered.

## Queueing events when the sessions response is slow

The Media Collection API is RESTful: you make an HTTP request and wait for the response. This matters most for the [sessions request](sessions.md), because the session ID it returns is required for all later calls. Your player may fire events before the sessions response returns. When that happens, queue any events that occur between the sessions request and its response. After the session ID arrives, process the queued events first, then continue sending live events.

<InlineAlert variant="note" slots="text"/>

The events endpoint does not return data to the client beyond an HTTP response code.

```js
let sessionId = null;
const pending = [];

function collectEvent(event) {
  if (!sessionId) {
    pending.push(event);   // No session ID yet: queue the event.
    return;
  }
  sendEvent(event);        // Session ID available: send immediately.
}

// Once the sessions response returns and sessionId is set, drain the queue:
function flushPending() {
  pending.forEach(sendEvent);
  pending.length = 0;
}
```

## Related content

* [Sessions endpoint](sessions.md)
* [Events endpoint](events.md)
* [Request parameters](parameters.md)
* [Custom metadata](custom-metadata.md)
