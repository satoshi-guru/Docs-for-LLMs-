---
title: ""
url: https://supabase.com/docs/guides/api/creating-routes
---

# 

REST API

#

Creating API Routes

* * *

API routes are automatically created when you create Postgres Tables, Views, or Functions.

## Create a table#

Let's create our first API route by creating a table called `todos` to store tasks. This creates a corresponding route `todos` which can accept `GET`, `POST`, `PATCH`, & `DELETE` requests.

DashboardSQL

  1. Go to the [Table editor](/dashboard/project/_/editor) page in the Dashboard.
  2. Click **New Table** and create a table with the name `todos`.
  3. Click **Save**.
  4. Click **New Column** and create a column with the name `task` and type `text`.
  5. Click **Save**.
  6. In the [**Integrations > Data API**](/dashboard/project/_/integrations/data_api/settings) section of the Dashboard, expose specific tables like `todos` or the functions you want to access. To automatically grant access for new tables and functions in `public`, enable **Default privileges for new entities**.


##### What it means to expose tables or functions via the API

Granting privileges (like `select` or `execute`) to roles such as `anon` or `authenticated` makes those tables or functions accessible through the Data API. Behind the scenes, the API checks your Postgres permissions—only objects with explicit grants are exposed, and all other access is denied by default.

## API URL and keys#

Every Supabase project has a unique API URL. Your API is secured behind an API gateway which requires an API Key for every request.

To do this, you need to get the Project URL and key from [the project's **Connect** dialog](/dashboard/project/_?showConnect=true).

##### Changes to API keys

Supabase is changing the way keys work to improve project security and developer experience. You can [read the full announcement](https://github.com/orgs/supabase/discussions/29260), but in the transition period, you can use both the current `anon` and `service_role` keys and the new publishable key with the form `sb_publishable_xxx` which will replace the older keys.

In most cases, you can get the correct key from [the Project's **Connect** dialog](/dashboard/project/_?showConnect=true), but if you want a specific key, you can find all keys in [the API Keys section of a Project's Settings page](/dashboard/project/_/settings/api-keys/):

  * **For legacy keys** , copy the `anon` key for client-side operations and the `service_role` key for server-side operations from the **Legacy API Keys** tab.
  * **For new keys** , open the **API Keys** tab, if you don't have a publishable key already, click **Create new API Keys** , and copy the value from the **Publishable key** section.


[Read the API keys docs](/docs/guides/getting-started/api-keys) for a full explanation of all key types and their uses.

The REST API is accessible through the URL `https://<project_ref>.supabase.co/rest/v1`

Both of these routes require the key to be passed through an `apikey` header.

## Using the API#

You can interact with your API directly via HTTP requests, or you can use the client libraries which we provide.

Let's see how to make a request to the `todos` table which we created in the first step, using the API URL (`SUPABASE_URL`) and Key (`SUPABASE_PUBLISHABLE_KEY`) we provided:

JavaScriptcURL


    1

    // Initialize the JS client

    2

    import { createClient } from '@supabase/supabase-js'

    3

    4

    const supabase = createClient(SUPABASE_URL, SUPABASE_PUBLISHABLE_KEY)

    5

    6

    // Make a request

    7

    const { data: todos, error } = await supabase.from('todos').select('*')

JS Reference: [`select()`](/docs/reference/javascript/select), [`insert()`](/docs/reference/javascript/insert), [`update()`](/docs/reference/javascript/update), [`upsert()`](/docs/reference/javascript/upsert), [`delete()`](/docs/reference/javascript/delete), [`rpc()`](/docs/reference/javascript/rpc) (call Postgres functions).

### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/api/creating-routes so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/api/creating-routes so I can ask questions about its contents)