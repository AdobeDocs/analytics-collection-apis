---
title: Troubleshoot bulk data uploads
description: Diagnose file-level and row-level failures, including HTTP response codes, for Bulk Data Insertion API uploads.
---

# Troubleshoot bulk data uploads

Use the following possible solutions to help determine why an upload failed.

<InlineAlert variant="warning" slots="text"/>

Adobe highly encourages you to use the [Validate](endpoints.md#validate) endpoint when establishing a BDIA workflow. If you do not first validate a file, you could end up with a combination of invalid and valid hits. The valid hits are processed, while the invalid hits are discarded. This scenario causes major issues with data quality, such as visit/visitor count and attribution.

This page is divided into sections for **issues with the file**, **issues with individual rows**, recovering from a failed upload, removing uploaded data, HTTP response codes, and product availability. Your course of action depends on the root of the issue.

## Issues with the file

If Adobe encounters an issue with the file as a whole, the upload fails entirely and no rows are processed. You can fix the file and upload it again to successfully ingest it into Adobe Analytics.

### Not in GZIP format

If a file is not in proper GZIP format, it results in the state of "File Error" and no rows are processed. Adobe recommends that the file creation process is checked to ensure that it properly compresses files.

```json
{
  "file_id":"3ae262c5-cdea-4bdb-a41c-fbd2c2004c4d",
  ...
  "status_code":"REJECTED",
  "processing_log":"An error occurred: Not in GZIP format",
  "error":"No hits were found in the file."
}
```

### Does not contain a visitor ID header column

A file must contain at least one visitor ID column. If you upload a file that does not contain any of the available visitor ID columns, the upload fails and no rows are processed. This error relates to missing column headers altogether, not individual rows missing required columns.

```json
{
  "file_id":"3ae262c5-cdea-4bdb-a41c-fbd2c2004c4d",
  ...
  "status_code":"REJECTED",
  "processing_log":"No visitor ID found in the file header.  There must be one of VisitorID, MarketingCloudVisitorID, IPAddress, or CustomerID defined...",
  "error":"No valid rows were found in the file."
}
```

### Missing timestamp

A file must contain the `timestamp` column. If you upload a file that does not contain the `timestamp` column, the upload fails and no rows are processed. This error relates to a missing column header, not individual rows missing a `timestamp` value.

```json
{
  "file_id":"3ae262c5-cdea-4bdb-a41c-fbd2c2004c4d",
  ...
  "status_code":"REJECTED",
  "processing_log":"No timestamp field found in the file header. Processing complete: 0 rows will be submitted. 5000 rows were invalid.",
  "error":"No valid rows were found in the file."
}
```

## Issues with individual rows

If Adobe encounters an issue with an individual row in an otherwise valid file, that row is skipped.

Isolating skipped rows and uploading them in a separate API call is not advised, because that means visitor data is uploaded out of order. Data uploaded in the wrong order can cause issues with data quality. All of these scenarios are costly and can easily be avoided if you use the `validate` endpoint before ingesting data into Adobe Analytics.

### Some rows missing values

If some rows have missing required values, those hits are skipped.

```json
{
  "file_id":"3ae262c5-cdea-4bdb-a41c-fbd2c2004c4d",
  ...
  "status_code":"UPLOADED",
  "processing_log":"On row: 1, missing 'UserAgent'. This row will not be submitted. On row: 57, missing 'ReportSuiteId'. This row will not be submitted. Processing complete: 4998 rows will be submitted. 2 rows were invalid."
}
```

### Inconsistent column count

If some rows have the wrong number of columns, those hits are skipped.

```json
{
  "file_id":"3ae262c5-cdea-4bdb-a41c-fbd2c2004c4d",
  ...
  "status_code":"UPLOADED",
  "processing_log":"On row: 1, inconsistent column count. Expected 5 columns, but found 6. On row: 3, inconsistent column count.  Expected 5 columns, but found 4. Processing complete: 4998 rows will be submitted.  2 rows were invalid."
}
```

## Recovering from a failed upload

If you did not validate a file before uploading it, your best course of action depends on how much of the file was ingested.

### The entire upload failed

If the entire file fails, use the sections above to determine the cause of the issue. You can then make adjustments to the file creation process, recreate the file, and reupload it. These actions do not result in any duplicate data, because no data was ingested into Analytics. Look at the `invalid_rows` and `rows` fields in the API response message to determine whether all of the rows failed. If `invalid_rows` is equal to `rows`, then no rows were successfully ingested.

### Some rows succeeded and others failed

If some hits were valid while others were not, your best course of action depends on how many invalid rows exist:

* **Mostly valid rows**: If a file with a large number of rows is submitted, but a small percentage of those rows fail, it is probably best to not resubmit the file. If you resubmit a file where most rows were successfully ingested during its initial processing, the majority of rows result in duplicated data in Analytics. Accepting that a small number of rows were lost is typically better than duplicating a larger amount of data.
* **Mostly invalid rows**: If a file is submitted and a large percentage of the rows fail, it might make sense to repair the rows and resubmit the file. Only take this action if the number of duplicate hits is acceptable and the missed server calls are individually significant. Otherwise, Adobe recommends fixing the file generation process and not trying to resubmit the file.

## Removing uploaded data

**Data uploaded through the Bulk Data Insertion API is permanent.** In some cases, you can use the [Data Repair API](https://developer.adobe.com/analytics-apis/docs/2.0/guides/endpoints/data-repair/), but Adobe strongly recommends that you validate uploads before ingesting them into Adobe Analytics. Adobe Engineering Services can also assist customers in removing undesired data through a paid service engagement. Contact your Adobe Account Team for more information.

## HTTP response codes

The following response codes are returned by the API:

| HTTP response | Description |
|--|--|
| `100 - Continue` | Used when uploading a file. This is sent to a client after authentication is checked and the HTTP request headers are validated. This signals to the client that they can begin to upload the large file. For example, if a client waits for this response code before sending a file, it can avoid uploading an entire file before learning that a visitor group ID was not specified. |
| `400 - Bad Request` | Required headers are missing, or the uploaded file is missing critical information or is malformed. |
| `401 - Unauthorized` | The API key or user token used to interact with the API is not valid. |
| `403 - Forbidden` | Occurs when attempting to perform an action that is not currently allowed. |
| `404 - Not Found` | Occurs when attempting to call an undefined endpoint. |
| `413 - Payload Too Large` | Returned when the file being uploaded is larger than the permitted size. |
| `429 - Too Many Requests` | Occurs when the number of API calls exceeds the system limits. |
| `500 - Internal Error` | Occurs when the API encounters an unexpected internal error that it is unable to recover from. |

## Product availability

BDIA is built with redundancy and safeguards to ensure that issues due to unexpected system failures are rare. If one occurs, Adobe's monitoring alerts on-call staff to address the availability issue as quickly as possible. All files received are stored safely server-side for ingestion once the system stabilizes.
