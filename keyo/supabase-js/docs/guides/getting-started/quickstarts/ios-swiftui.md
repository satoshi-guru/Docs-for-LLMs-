---
title: ""
url: https://supabase.com/docs/guides/getting-started/quickstarts/ios-swiftui
---

# 

Getting Started

#

Use Supabase with iOS and SwiftUI

##

Learn how to create a Supabase project, add some sample data to your database, and query the data from an iOS app.

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

### Create an iOS SwiftUI app with Xcode

Open Xcode > New Project > iOS > App. You can skip this step if you already have a working app.

3

### Install the Supabase client library

Add the [supabase-swift](https://github.com/supabase/supabase-swift) package to your app using the Swift Package Manager.

In Xcode, navigate to **File > Add Package Dependencies...** and enter the repository URL `https://github.com/supabase/supabase-swift` in the search bar. For detailed instructions, see Apple's [tutorial on adding package dependencies](https://developer.apple.com/documentation/xcode/adding-package-dependencies-to-your-app).

Make sure to add `Supabase` product package as a dependency to your application target.

4

### Initialize the Supabase client

Create a new `Supabase.swift` file add a new Supabase instance using your project URL and publishable key:

###### Project URL

No project found

###### Publishable key

No project found

###### Supabase.swift


    1

    import Supabase

    2

    3

    let supabase = SupabaseClient(

    4

      supabaseURL: URL(string: "YOUR_SUPABASE_URL")!,

    5

      supabaseKey: "YOUR_SUPABASE_PUBLISHABLE_KEY"

    6

    )

### Get API details#

Now that you've created some database tables, you are ready to insert data using the auto-generated API.

To do this, you need to get the Project URL and key from [the project **Connect** dialog](/dashboard/project/_?showConnect=true&connectTab=mobiles&framework=swift).

[Read the API keys docs](/docs/guides/getting-started/api-keys) for a full explanation of all key types and their uses.

##### Changes to API keys

Supabase is changing the way keys work to improve project security and developer experience. You can [read the full announcement on GitHub](https://github.com/orgs/supabase/discussions/29260).

The older `anon` and `service_role` keys will work until the end of 2026 but **we strongly encourage switching to and using** the new publishable (`sb_publishable_xxx`) and secret (`sb_secret_xxx`) keys now.

In most cases, you can get keys from [the Project's **Connect** dialog](/dashboard/project/_?showConnect=true&connectTab=mobiles&framework=swift), but if you want a specific key, you can find them in the [**Settings > API Keys**](/dashboard/project/_/settings/api-keys/) section of the Dashboard.

  * **For legacy keys** , copy the `anon` key for client-side operations and the `service_role` key for server-side operations from the **Legacy API Keys** tab.
  * **For new keys** , open the **API Keys** tab, if you don't have a publishable key already, click **Create new API Keys** , and copy the value from the **Publishable key** section.


5

### Create a data model for instruments

Create a decodable struct to deserialize the data from the database.

Add the following code to a new file named `Instrument.swift`.

###### Instrument.swift


    1

    struct Instrument: Decodable, Identifiable {

    2

      let id: Int

    3

      let name: String

    4

    }

6

### Query data from the app

Use a `task` to fetch the data from the database and display it using a `List`.

Replace the default `ContentView` with the following code.

###### ContentView.swift


    1

    import SwiftUI

    2

    3

    struct ContentView: View {

    4

    5

      @State var instruments: [Instrument] = []

    6

    7

      var body: some View {

    8

        List(instruments) { instrument in

    9

          Text(instrument.name)

    10

        }

    11

        .overlay {

    12

          if instruments.isEmpty {

    13

            ProgressView()

    14

          }

    15

        }

    16

        .task {

    17

          do {

    18

            instruments = try await supabase.from("instruments").select().execute().value

    19

          } catch {

    20

            dump(error)

    21

          }

    22

        }

    23

      }

    24

    }

7

### Start the app

Run the app on a simulator or a physical device by hitting `Cmd + R` on Xcode.

## Setting up deep links#

If you want to implement authentication features like magic links or OAuth, you need to set up deep links to redirect users back to your app. For instructions on configuring custom URL schemes for your iOS app, see the [deep linking guide](/docs/guides/auth/native-mobile-deep-linking?platform=swift).

## Next steps#

  * Learn how to build a complete user management app with authentication in the [Swift tutorial](/docs/guides/getting-started/tutorials/with-swift)
  * Explore the [supabase-swift](https://github.com/supabase/supabase-swift) library on GitHub


### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/getting-started/quickstarts/ios-swiftui so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/getting-started/quickstarts/ios-swiftui so I can ask questions about its contents)