---
title: Authentication
description: Set up the OAuth Server-to-Server credential that the Bulk Data Insertion API requires.
---

# Authentication

The Bulk Data Insertion API (BDIA) is the only Adobe Analytics data collection method that requires authentication. It uses an OAuth Server-to-Server credential. All other collection methods ([Data Insertion API](../data-insertion/index.md) + [Media Collection API](../media-collection/index.md)) are unauthenticated.

## Create an OAuth Server-to-Server credential

Before you make calls, create an API project and credential in the Adobe Developer Console:

1. Log in to the [Adobe Developer Console](https://developer.adobe.com/console).
1. Navigate to **Projects**, and select the desired project or [create one](https://developer.adobe.com/developer-console/docs/guides/projects/projects-empty).
1. Add the **Adobe Analytics** API to the project, with access to the report suites you plan to send data to.
1. Under **Credentials**, select **OAuth Server-to-Server**.

## Authentication headers

Each Bulk Data Insertion API call then includes two authentication headers:

* **`Authorization`** — A bearer access token, in the format `Bearer {ACCESS_TOKEN}`. Generate a token with the **Generate access token** button in the Developer Console, or programmatically as described in the [Server-to-Server authentication guide](https://developer.adobe.com/developer-console/docs/guides/authentication/ServerToServerAuthentication/).
* **`x-api-key`** — The **Client ID** (API key) of your OAuth Server-to-Server credential, found under **Credentials** in the Developer Console.

Access tokens expire. Generate a new token before the current one expires, as described in the [Server-to-Server authentication guide](https://developer.adobe.com/developer-console/docs/guides/authentication/ServerToServerAuthentication/). For the endpoint-specific headers that accompany these credentials — such as `x-adobe-vgid` — see [Bulk Data Insertion API endpoints](endpoints.md).
