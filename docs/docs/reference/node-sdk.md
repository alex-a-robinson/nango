---
sidebar_label: 'Node SDK'
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Nango Node SDK

The Nango node SDK helps you retrieve access tokens and work with [Connections](reference/core-concepts.md#connections) from your backend.

## Installing & instantiating the node SDK

The node SDK is [available on NPM](https://www.npmjs.com/package/@nangohq/node) as `@nangohq/node`, install it with your favorite package manager:

```bash
npm i -S @nangohq/node
```

How you instantiate the SDK depends on your Nango deployment method:
<Tabs groupId="deployment" queryString>
<TabItem value="cloud" label="Nango Cloud">

You will need the `Secret Key` from your [Dashboard's project settings](https://app.nango.dev/project-settings).

```js
import { Nango } from '@nangohq/node';

let nango = new Nango({ secretKey: '<SECRET-KEY>' });
```

  </TabItem>
  <TabItem value="localhost" label="Localhost">

```js
import { Nango } from '@nangohq/node';

let nango = new Nango({ host: 'http://localhost:3003' });
```

  </TabItem>
  <TabItem value="self-hosted" label="Self-hosted">

```js
import { Nango } from '@nangohq/node';

let nango = new Nango({ host: '<NANGO-HOST-AND-PORT>' });
```

  </TabItem>
</Tabs>

If you are using TypeScript you will notice that the node SDK ships with TypeScript types.

## Getting the current access token for a Connection

```ts
let access_token = await nango.getToken('<PROVIDER-CONFIG-KEY>', '<CONNECTION-ID>');
console.log(access_token); // For OAuth 2 APIs this will print "axb_dijifoweif32qwij923jfo3" or similar
```

If you are not familiar with the Provider Config Key and Connection Id parameters please take a look at the [Core Concepts](reference/core-concepts.md) page.

This is the recommended way to retrieve an access token to make an API call.

**Response for OAuth 1 tokens**  
If the access token stored in Nango is for an OAuth 1 provider (there are very very few left, the most prominent ones are Twitter and Trello) the response is an object instead of a string:

```js
{
    oAuthToken: "efjweiofoj34iji2io43j",
    oAuthTokenSecret: "dijdwojfwef89329834hf3ohf2o3i8"
}
```

This is because OAuth 1 requires you to cryptographically sign every HTTP request, which requires both an access token and an (OAuth 1 specific) access token secret.

:::tip Keep your access tokens fresh and don't cache them!
When you call this method the Nango server will check whether the access token needs to be refreshed, and, if needed, refresh it before returning it to you. This typically changes the access token.

Because of this it is important that you always use this method to get the latest access token from Nango just before making an API request. If you cache the access token on your end you risk working with an expired or revoked access token and your API call will fail!

We take great care to make sure that this call to get an access token is blazingly fast, so retrieving it fresh will not slow down your API requests.
:::

## Getting Connection Details & Metadata {#connectionDetails}

```js
let connectionDetails = await nango.getConnection('<CONFIG-KEY>', '<CONNECTION-ID>');
```

This retrieves the full Connection object from Nango, which looks like this:

```js
{
id: 18393,                                  // Nango internal connection id
created_at: '2023-03-08T09:43:03.725Z',     // Creation timestamp
updated_at: '2023-03-08T09:43:03.725Z',     // Last updated timestamp (e.g. last token refresh)
provider_config_key: 'github',              // <PROVIDER-CONFIG-KEY>
connection_id: '1',                         // <CONNECTION-ID>
credentials: {
    type: 'OAUTH2',                         // OAUTH2 or OAUTH1
    access_token: 'gho_tsXLG73f....',       // The current access token (refreshed if needed)
    refresh_token: 'gho_fjofu84u9....',     // Refresh token (if available, otherwise missing)
    expires_at: '2024-03-08T09:43:03.725Z', // Expiration date of access token (only if refresh token is present, otherwise missing)
    raw: {                                  // Raw token response from the OAuth provider: Contents vary!
        access_token: 'gho_tsXLG73f....',
        token_type: 'bearer',
        scope: 'public_repo,user'
    }
},
connection_config: {},                      // Additional configuration, see Frontend SDK reference
account_id: 0,                              // ID of your Nango account (Nango Cloud only)
metadata: {}                                // Structured metadata retrieved by Nango
}
```

The metadata field contains [structured metadata](reference/core-concepts.md#metadata), which Nango obtained from the OAuth flow. This varies by provider and is documented on the provider's Nango documentation page.

## Accessing the raw token response

```js
let rawTokenResponse = await nango.getRawTokenResponse('<CONFIG-KEY>', '<CONNECTION-ID>');
```

A fast access method for obtaining the raw token response, as returned by the OAuth provider, for a given connection. This is equivalent to getting the full connection details and then reading `fullDetails.credentials.raw`.

## Getting a list of all Connections

```js
let connectionsList = await nango.listConnections();
```

Returns a list of minimalistic Connection objects. This can be helpful if you need to check whether a user has setup a specific integration. Note that the list does not contain any access credentials and fetching it also does not refresh the access tokens of any Connections.

The return value looks like this:

```js
{
    connections: [
        {
            connection_id: '<CONNECTION-ID-1>',
            provider: '<CONFIG-KEY-1>',
            created: '2023-03-08T09:43:03.725Z'
        },
        {
            //....
        }
        // Additional objects like the one above
    ];
}
```

## Getting a list of all Connections for a specific connectionId

In the case where you want to get a list of all the connections for only a specific connectionId you can pass the connection ID
```js
let connectionsList = await nango.listConnections("userId");
```

```js
{
    connections: [
        {
            connection_id: 'userId',
            provider: '<CONFIG-KEY-1>',
            created: '2023-03-08T09:43:03.725Z'
        },
        {
            //....
        }
        // Additional objects like the one above but only for the same connection Id
    ];
}
```
