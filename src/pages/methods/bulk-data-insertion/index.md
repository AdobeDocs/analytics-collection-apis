---
title: Bulk Data Insertion API
description: Upload batches of server-call data to Adobe Analytics as files using the Bulk Data Insertion API.
---

# Bulk Data Insertion API

The Bulk Data Insertion API (BDIA) is an Adobe Analytics capability that lets you upload server call data in batches of files instead of using client-side libraries such as AppMeasurement. The server calls in these batch files can be either current (live) data or historical data. It is a more scalable successor to the Data Insertion API in previous versions of the Adobe Analytics API.

Bulk Data Insertion solves several problems for a variety of use cases. Some use case examples include:

* Ingesting historical data from a previous analytics system.
* An internal analytics collection system that makes it unfeasible to use AppMeasurement. You can use Extract-Transform-Load (ETL) processes to put data into batch files, then use BDIA to upload them to Adobe Analytics.
* Data collection from devices that have only intermittent connectivity to the internet. These devices store up the interactions until they receive a connection. The device can then upload the data all at once through BDIA.

<InlineAlert variant="info" slots="text"/>

Adobe may add optional request and response members (name/value pairs) to existing API objects at any time and without notice or changes in versioning. Adobe recommends that you refer to the API documentation of any third-party tool you integrate with our APIs so that such additions are ignored in processing if not understood. If implemented properly, such additions are non-breaking changes for your implementation. Adobe will not remove parameters or add required parameters without first providing standard notification through release notes.

## When to use this API

The Data Insertion API and Bulk Data Insertion API are both methods to submit server-side data to Adobe Analytics. Data Insertion API calls are made one event at a time, while the Bulk Data Insertion API accepts CSV-formatted files that contain event data, one event per row. As a bulk service, BDIA is optimized for larger files sent less frequently. If your use case requires sending more than one file per second, or you need to send events individually as they occur, use the [Data Insertion API](../data-insertion/index.md) instead. For a comparison across all Adobe Analytics collection methods, see the [collection method comparison](../../index.md#compare-each-method).

## Prerequisites

Before using this API, make sure that all of the following are met:

* You can successfully authenticate with the API using OAuth Server-to-Server credentials. See [Authentication](authentication.md) to make sure that you have the correct permissions and have created an API client on Adobe Developer.
* The desired report suite is timestamp-enabled or timestamp optional. See [Timestamps optional](https://experienceleague.adobe.com/en/docs/analytics/technotes/timestamps-optional) in the Adobe Analytics documentation. All newly created report suites are set to timestamp optional by default.
* Communicate to Adobe the expected volume of ingestion per day. Based on this information, Adobe provisions the appropriate hardware to handle that volume and creates a per-second throttle limit. If enough files are uploaded in a short amount of time to exceed the throttle limit, Adobe ingests uploaded files more slowly. These limits help ensure timely processing and availability of data for reporting. They also help protect the system from becoming overwhelmed before proper capacity is provisioned for a sharp increase in file uploads.
* Follow the established [file formatting requirements](file-format.md) for each upload.
* If using a Customer Attribute as a seed to automatically generate an ECID, provisioning by Adobe is required first. See [Use a customer ID to identify visitors](customer-id.md).
* You are using the correct number of [visitor groups](visitor-groups.md) for your anticipated load. Follow the guidelines on file send frequency limits to avoid having your requests throttled or your data processed out of order.
* You do not plan to upload more than one file per second. If rates above this level are needed, consider using the [Data Insertion API](../data-insertion/index.md) instead.

Once you meet all prerequisites, see [File format](file-format.md) to prepare your data in a format usable by the API.
