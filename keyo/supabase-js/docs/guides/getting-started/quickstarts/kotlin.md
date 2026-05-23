---
title: ""
url: https://supabase.com/docs/guides/getting-started/quickstarts/kotlin
---

# 

Getting Started

#

Use Supabase with Android Kotlin

##

Learn how to create a Supabase project, add some sample data to your database, and query the data from an Android Kotlin app.

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

### Create an Android app with Android Studio

Open Android Studio > New > New Android Project.

3

### Install the Dependencies

Open `build.gradle.kts` (app) file and add the serialization plug, Ktor client, and Supabase client.

Replace the version placeholders `$kotlin_version` with the Kotlin version of the project, and `$supabase_version` and `$ktor_version` with the respective latest versions.

The latest supabase-kt version can be found [here](https://github.com/supabase-community/supabase-kt/releases) and Ktor version can be found [here](https://ktor.io/docs/welcome.html).


    1

    plugins {

    2

      ...

    3

      kotlin("plugin.serialization") version "$kotlin_version"

    4

    }

    5

    ...

    6

    dependencies {

    7

      ...

    8

      implementation(platform("io.github.jan-tennert.supabase:bom:$supabase_version"))

    9

      implementation("io.github.jan-tennert.supabase:postgrest-kt")

    10

      implementation("io.ktor:ktor-client-android:$ktor_version")

    11

    }

4

### Add internet access permission

Add the following line to the `AndroidManifest.xml` file under the `manifest` tag and outside the `application` tag.


    1

    ...

    2

    <uses-permission android:name="android.permission.INTERNET" />

    3

    ...

5

### Initialize the Supabase client

You can create a Supabase client whenever you need to perform an API call.

For the sake of simplicity, we will create a client in the `MainActivity.kt` file at the top just below the imports.

Replace the `supabaseUrl` and `supabaseKey` with your own:

###### Project URL

No project found

###### Publishable key

No project found


    1

    import ...

    2

    3

    val supabase = createSupabaseClient(

    4

        supabaseUrl = "https://xyzcompany.supabase.co",

    5

        supabaseKey = "your_publishable_key"

    6

      ) {

    7

        install(Postgrest)

    8

    }

    9

    ...

### Get API details#

Now that you've created some database tables, you are ready to insert data using the auto-generated API.

To do this, you need to get the Project URL and key from [the project **Connect** dialog](/dashboard/project/_?showConnect=true&connectTab=mobiles&framework=androidkotlin).

[Read the API keys docs](/docs/guides/getting-started/api-keys) for a full explanation of all key types and their uses.

##### Changes to API keys

Supabase is changing the way keys work to improve project security and developer experience. You can [read the full announcement on GitHub](https://github.com/orgs/supabase/discussions/29260).

The older `anon` and `service_role` keys will work until the end of 2026 but **we strongly encourage switching to and using** the new publishable (`sb_publishable_xxx`) and secret (`sb_secret_xxx`) keys now.

In most cases, you can get keys from [the Project's **Connect** dialog](/dashboard/project/_?showConnect=true&connectTab=mobiles&framework=androidkotlin), but if you want a specific key, you can find them in the [**Settings > API Keys**](/dashboard/project/_/settings/api-keys/) section of the Dashboard.

  * **For legacy keys** , copy the `anon` key for client-side operations and the `service_role` key for server-side operations from the **Legacy API Keys** tab.
  * **For new keys** , open the **API Keys** tab, if you don't have a publishable key already, click **Create new API Keys** , and copy the value from the **Publishable key** section.


6

### Create a data model for instruments

Create a serializable data class to represent the data from the database.

Add the following below the `createSupabaseClient` function in the `MainActivity.kt` file.


    1

    @Serializable

    2

    data class Instrument(

    3

        val id: Int,

    4

        val name: String,

    5

    )

7

### Query data from the app

Use `LaunchedEffect` to fetch data from the database and display it in a `LazyColumn`.

Replace the default `MainActivity` class with the following code.

Note that we are making a network request from our UI code. In production, you should probably use a `ViewModel` to separate the UI and data fetching logic.


    1

    class MainActivity : ComponentActivity() {

    2

        override fun onCreate(savedInstanceState: Bundle?) {

    3

            super.onCreate(savedInstanceState)

    4

            setContent {

    5

                SupabaseTutorialTheme {

    6

                    // A surface container using the 'background' color from the theme

    7

                    Surface(

    8

                        modifier = Modifier.fillMaxSize(),

    9

                        color = MaterialTheme.colorScheme.background

    10

                    ) {

    11

                        InstrumentsList()

    12

                    }

    13

                }

    14

            }

    15

        }

    16

    }

    17

    18

    @Composable

    19

    fun InstrumentsList() {

    20

        var instruments by remember { mutableStateOf<List<Instrument>>(listOf()) }

    21

        LaunchedEffect(Unit) {

    22

            withContext(Dispatchers.IO) {

    23

                instruments = supabase.from("instruments")

    24

                                  .select().decodeList<Instrument>()

    25

            }

    26

        }

    27

        LazyColumn {

    28

            items(

    29

                instruments,

    30

                key = { instrument -> instrument.id },

    31

            ) { instrument ->

    32

                Text(

    33

                    instrument.name,

    34

                    modifier = Modifier.padding(8.dp),

    35

                )

    36

            }

    37

        }

    38

    }

8

### Start the app

Run the app on an emulator or a physical device by clicking the `Run app` button in Android Studio.

### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/getting-started/quickstarts/kotlin so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/getting-started/quickstarts/kotlin so I can ask questions about its contents)