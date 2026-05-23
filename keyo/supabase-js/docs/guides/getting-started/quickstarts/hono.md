---
title: ""
url: https://supabase.com/docs/guides/getting-started/quickstarts/hono
---

# 

Getting Started

#

Use Supabase with Hono

##

Learn how to create a Supabase project, add some sample data to your database, secure it with auth, and query the data from a Hono app.

* * *

1

### Create a Hono app

Bootstrap the Hono example app from the Supabase Samples using the CLI.

###### Terminal


    1

    npx supabase@latest bootstrap hono

2

### Install the Supabase client library

The `package.json` file in the project includes the necessary dependencies, including `@supabase/supabase-js` and `@supabase/ssr` to help with server-side auth.

###### Terminal


    1

    npm install

3

### Set up the required environment variables

Copy the `.env.example` file to `.env` and update the values with your Supabase project URL and publishable key.

Lastly, [enable anonymous sign-ins](/dashboard/project/_/auth/providers) in the Auth settings.

###### Project URL

No project found

###### Publishable key

No project found

###### Terminal


    1

    cp .env.example .env

### Get API details#

Now that you've created some database tables, you are ready to insert data using the auto-generated API.

To do this, you need to get the Project URL and key from [the project **Connect** dialog](/dashboard/project/_?showConnect=true&connectTab=frameworks&framework=).

[Read the API keys docs](/docs/guides/getting-started/api-keys) for a full explanation of all key types and their uses.

##### Changes to API keys

Supabase is changing the way keys work to improve project security and developer experience. You can [read the full announcement on GitHub](https://github.com/orgs/supabase/discussions/29260).

The older `anon` and `service_role` keys will work until the end of 2026 but **we strongly encourage switching to and using** the new publishable (`sb_publishable_xxx`) and secret (`sb_secret_xxx`) keys now.

In most cases, you can get keys from [the Project's **Connect** dialog](/dashboard/project/_?showConnect=true&connectTab=frameworks&framework=), but if you want a specific key, you can find them in the [**Settings > API Keys**](/dashboard/project/_/settings/api-keys/) section of the Dashboard.

  * **For legacy keys** , copy the `anon` key for client-side operations and the `service_role` key for server-side operations from the **Legacy API Keys** tab.
  * **For new keys** , open the **API Keys** tab, if you don't have a publishable key already, click **Create new API Keys** , and copy the value from the **Publishable key** section.


4

### Start the app

Start the app, go to <http://localhost:5173>.

Learn how [server side auth](/docs/guides/auth/server-side/creating-a-client?queryGroups=framework&framework=hono) works with Hono.

###### Terminal


    1

    npm run dev

## Next steps#

  * Learn how [server side auth](/docs/guides/auth/server-side/creating-a-client?queryGroups=framework&framework=hono) works with Hono.
  * [Insert more data](/docs/guides/database/import-data) into your database
  * Upload and serve static files using [Storage](/docs/guides/storage)


### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/getting-started/quickstarts/hono so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/getting-started/quickstarts/hono so I can ask questions about its contents)