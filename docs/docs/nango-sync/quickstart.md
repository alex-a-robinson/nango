---
sidebar_label: 'Quickstart 🚀'
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Quickstart (Nango Sync)

**Goal**  
Show you Nango Sync in action by syncing a list of all Pokémons to your local DB **in 3 minutes**.

**Prerequisite**  
Before we dive in, make sure you have a recent version of [Docker](https://www.docker.com/products/docker-desktop/) installed.

Ready? Let's go!

## Step 1: Download & run Nango Sync

In a directory of your choice run:

```bash
git clone https://github.com/NangoHQ/nango-sync.git
```

And then start Nango Sync:

```bash
cd nango-sync && docker compose up  # cd nango-sync && docker-compose up if you are on an older version of docker
```

## Step 2: Create a new Sync

Next we need to tell Nango Sync about our Sync job: Here we want it to fetch the latest details about all Pokémons from the [Poké API](https://pokeapi.co/) and sync it to our local database.

To do this run the following CURL command in the terminal (we use Nango Sync's REST API here to create a new Sync):

```bash
  curl --request POST \
    --url http://localhost:3003/v1/syncs \
    --header "Content-type: application/json" \
    --data '{"url": "https://pokeapi.co/api/v2/pokemon", "response_path": "results", "paging_url_path":"next", "mapped_table":"pokemons", "frequency":"1 minute"}'
```

That's it! You have just created your 1st Sync :)

While CURL is great for testing things locally, in your code you will probably prefer using one of our native SDKs to create syncs on the fly (e.g. when a user activates a new native integration).

Here is what the above CURL command looks like with our SDKs:
<Tabs groupId="programming-language">

  <TabItem value="node" label="Node SDK">

```js
import { Nango } from '@nangohq/node-client';

let config = {
    response_path: 'results', // For finding records in the API response.
    paging_url_path: 'next', // For finding pagination data in responses.
    mapped_table: 'pokemons', // We want the data to be synced to a table called "pokemons"
    frequency: '1 minute' // How often the data is refreshed from the external API.
};

let res = await new Nango().sync('https://pokeapi.co/api/v2/pokemon', config);

console.log(res.data);
```

  </TabItem>
</Tabs>

## Step 3: Inspect the synced ~~data~~ Pokémons

The Sync you just created will keep your Pokémon list up-to-date forever.  
View [the list of all Pokémons](http://localhost:8080/?pgsql=nango-db&username=nango&db=nango&ns=nango&select=pokemons) in your local db (password is `nango`).

## That's it!

Congratulations, you now have a local copy of all Pokémons in your database 🎉  
It's yours to keep.

Whilst this is just a small toy example we hope it has shown you how Nango Sync can create powerful syncs with a single line of code.

When you are ready here are some suggestions to get the most out of Nango Sync:

-   [Explore the core concepts](nango-sync/use-nango/core-concepts.md) - our recommended way to get started and learn more about Nango
-   [Join the Slack Community](https://nango.dev/slack) and give us feedback on the quickstart & Nango
-   [Explore more examples](real-world-examples.md)
-   [Explore the Architecture](architecture.md)
-   [Contribute ❤️](contributing.md)
