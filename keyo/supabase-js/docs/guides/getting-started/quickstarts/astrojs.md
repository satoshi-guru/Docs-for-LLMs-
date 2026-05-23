---
title: ""
url: https://supabase.com/docs/guides/getting-started/quickstarts/astrojs
---

# 

Getting Started

#

Use Supabase with Astro

##

Learn how to create a Supabase project, add sample data, and query from an Astro app.

* * *

1

### Create a Supabase project

Go to [database.new](https://database.new) and create a new Supabase project.

Alternatively, you can create a project using the Management API:


    1

    # First, get your access token from https://supabase.com/dashboard/account/tokens

    2

    export SUPABASE_ACCESS_TOKEN="your-access-token"

    3

    4

    # List your organizations to get the organization ID

    5

    curl -H "Authorization: Bearer $SUPABASE_ACCESS_TOKEN" \

    6

      https://api.supabase.com/v1/organizations

    7

    8

    # Create a new project (replace <org-id> with your organization ID)

    9

    curl -X POST https://api.supabase.com/v1/projects \

    10

      -H "Authorization: Bearer $SUPABASE_ACCESS_TOKEN" \

    11

      -H "Content-Type: application/json" \

    12

      -d '{

    13

        "organization_id": "<org-id>",

    14

        "name": "My Project",

    15

        "region": "us-east-1",

    16

        "db_pass": "<your-secure-password>"

    17

      }'

###

When your project is up and running, go to the [**Table Editor**](/dashboard/project/_/editor) section of the Dashboard, create a new table and insert some data. Then in the [**Integrations > Data API**](/dashboard/project/_/integrations/data_api/settings) section of the Dashboard, expose the specific tables or functions you want to access. To automatically grant access for new tables and functions in `public`, enable **Default privileges for new entities**.

Alternatively, you can run the following snippet in your project's [SQL Editor](/dashboard/project/_/sql/new).

This creates an `instruments` table with some sample data, sets a secure baseline by setting only the privileges each Postgres role needs, and adds [Row Level Security (RLS)](/docs/guides/database/postgres/row-level-security) for enhanced security for database data by default.


    1

    -- Create the table

    2

    create table instruments (

    3

      id bigint primary key generated always as identity,

    4

      name text not null

    5

    );

    6

    7

    -- Insert sample data into the table

    8

    insert into instruments (name)

    9

    values

    10

      ('violin'),

    11

      ('viola'),

    12

      ('cello');

    13

    14

    -- Grant the privileges the role needs, which is read access

    15

    grant select on public.instruments to anon;

    16

    17

    -- Enable row level security for the table

    18

    alter table instruments enable row level security;

###

Create an RLS policy to make the data in your table publicly readable:


    1

    -- Create a policy to allow the anon role to read from the instruments table

    2

    create policy "public can read instruments"

    3

    on public.instruments

    4

    for select to anon

    5

    using (true);

2

### Create an Astro app

  * Create an Astro app using the `npm create` command.

##### Explore drop-in UI components for your Supabase app.

UI components built on shadcn/ui that connect to Supabase via a single command.

[Explore Components](https://supabase.com/ui)


###### Terminal


    1

    npm create astro@latest my-app

    2

    cd my-app

3

### Install Supabase client library and Node adapter

Install the `supabase-js` client library and the `@astrojs/node` adapter to enable server-side rendering.

###### Terminal


    1

    npm install @supabase/supabase-js @astrojs/node

4

### Configure Astro for SSR

Update your `astro.config.mjs`.

###### astro.config.mjs


    1

    import { defineConfig } from "astro/config";

    2

    import node from "@astrojs/node";

    3

    4

    export default defineConfig({

    5

      output: "server",

    6

      adapter: node({

    7

        mode: "standalone",

    8

      }),

    9

    });

5

### Declare Supabase Environment Variables

Create a `.env.local` file and populate with your Supabase connection variables:

###### Project URL

No project found

###### Publishable key

No project found

.env.local


    1

    PUBLIC_SUPABASE_URL=<SUBSTITUTE_SUPABASE_URL>

    2

    PUBLIC_SUPABASE_PUBLISHABLE_KEY=<SUBSTITUTE_SUPABASE_PUBLISHABLE_KEY>

### Get API details#

Now that you've created some database tables, you are ready to insert data using the auto-generated API.

To do this, you need to get the Project URL and key from [the project **Connect** dialog](/dashboard/project/_?showConnect=true&connectTab=frameworks&framework=astro).

[Read the API keys docs](/docs/guides/getting-started/api-keys) for a full explanation of all key types and their uses.

##### Changes to API keys

Supabase is changing the way keys work to improve project security and developer experience. You can [read the full announcement on GitHub](https://github.com/orgs/supabase/discussions/29260).

The older `anon` and `service_role` keys will work until the end of 2026 but **we strongly encourage switching to and using** the new publishable (`sb_publishable_xxx`) and secret (`sb_secret_xxx`) keys now.

In most cases, you can get keys from [the Project's **Connect** dialog](/dashboard/project/_?showConnect=true&connectTab=frameworks&framework=astro), but if you want a specific key, you can find them in the [**Settings > API Keys**](/dashboard/project/_/settings/api-keys/) section of the Dashboard.

  * **For legacy keys** , copy the `anon` key for client-side operations and the `service_role` key for server-side operations from the **Legacy API Keys** tab.
  * **For new keys** , open the **API Keys** tab, if you don't have a publishable key already, click **Create new API Keys** , and copy the value from the **Publishable key** section.


6

### Create a Supabase client helper

Create a utility file to initialize the Supabase client:

###### src/lib/supabase.ts


    1

    import { createClient } from "@supabase/supabase-js";

    2

    3

    const supabaseUrl = import.meta.env.PUBLIC_SUPABASE_URL

    4

    const supabasePublishableKey = import.meta.env.PUBLIC_SUPABASE_PUBLISHABLE_KEY

    5

    6

    export function createServerClient() {

    7

      return createClient(

    8

        supabaseUrl,

    9

        supabasePublishableKey

    10

      );

    11

    }

7

### Query Supabase data from Astro

Create a new file at `src/pages/instruments.astro` and populate with the following.

This queries all rows from the `instruments` table in Supabase and renders them on the page.

###### src/pages/instruments.astro


    1

    ---

    2

    import { createServerClient } from "../lib/supabase";

    3

    4

    const supabase = createServerClient();

    5

    const { data: instruments } = await supabase.from("instruments").select();

    6

    ---

    7

    8

    <html>

    9

      <head>

    10

        <title>Instruments</title>

    11

      </head>

    12

      <body>

    13

        <ul>

    14

          {instruments?.map((instrument) => (

    15

            <li>{instrument.name}</li>

    16

          ))}

    17

        </ul>

    18

      </body>

    19

    </html>

8

### Start the app

Run the development server, go to <http://localhost:4321/instruments> in your browser of choice to check the list of instruments.

###### Terminal


    1

    npm run dev

## Next steps#

  * Set up [Auth](/docs/guides/auth) for your app
  * [Insert more data](/docs/guides/database/import-data) into your database
  * Upload and serve static files using [Storage](/docs/guides/storage)


### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/getting-started/quickstarts/astrojs so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/getting-started/quickstarts/astrojs so I can ask questions about its contents)