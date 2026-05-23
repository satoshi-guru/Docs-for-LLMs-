---
title: ""
url: https://supabase.com/docs/guides/getting-started/quickstarts/flutter
---

# 

Getting Started

#

Use Supabase with Flutter

##

Learn how to create a Supabase project, add some sample data to your database, and query the data from a Flutter app.

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

### Create a Flutter app

Create a Flutter app using the `flutter create` command. You can skip this step if you already have a working app.

###### Terminal


    1

    flutter create my_app

3

### Install the Supabase client library

The fastest way to get started is to use the [`supabase_flutter`](https://pub.dev/packages/supabase_flutter) client library which provides a convenient interface for working with Supabase from a Flutter app.

Open the `pubspec.yaml` file inside your Flutter app and add `supabase_flutter` as a dependency.

###### pubspec.yaml


    1

    supabase_flutter: ^2.0.0

4

### Initialize the Supabase client

Open `lib/main.dart` and edit the main function to initialize Supabase using your project URL and publishable key:

###### Project URL

No project found

###### Publishable key

No project found

###### lib/main.dart


    1

    import 'package:supabase_flutter/supabase_flutter.dart';

    2

    3

    Future<void> main() async {

    4

      WidgetsFlutterBinding.ensureInitialized();

    5

    6

      await Supabase.initialize(

    7

        url: 'YOUR_SUPABASE_URL',

    8

        anonKey: 'YOUR_SUPABASE_PUBLISHABLE_KEY',

    9

      );

    10

      runApp(MyApp());

    11

    }

### Get API details#

Now that you've created some database tables, you are ready to insert data using the auto-generated API.

To do this, you need to get the Project URL and key from [the project **Connect** dialog](/dashboard/project/_?showConnect=true&connectTab=mobiles&framework=flutter).

[Read the API keys docs](/docs/guides/getting-started/api-keys) for a full explanation of all key types and their uses.

##### Changes to API keys

Supabase is changing the way keys work to improve project security and developer experience. You can [read the full announcement on GitHub](https://github.com/orgs/supabase/discussions/29260).

The older `anon` and `service_role` keys will work until the end of 2026 but **we strongly encourage switching to and using** the new publishable (`sb_publishable_xxx`) and secret (`sb_secret_xxx`) keys now.

In most cases, you can get keys from [the Project's **Connect** dialog](/dashboard/project/_?showConnect=true&connectTab=mobiles&framework=flutter), but if you want a specific key, you can find them in the [**Settings > API Keys**](/dashboard/project/_/settings/api-keys/) section of the Dashboard.

  * **For legacy keys** , copy the `anon` key for client-side operations and the `service_role` key for server-side operations from the **Legacy API Keys** tab.
  * **For new keys** , open the **API Keys** tab, if you don't have a publishable key already, click **Create new API Keys** , and copy the value from the **Publishable key** section.


5

### Query data from the app

Use a `FutureBuilder` to fetch the data when the home page loads and display the query result in a `ListView`.

Replace the default `MyApp` and `MyHomePage` classes with the following code.

###### lib/main.dart


    1

    class MyApp extends StatelessWidget {

    2

      const MyApp({super.key});

    3

    4

      @override

    5

      Widget build(BuildContext context) {

    6

        return const MaterialApp(

    7

          title: 'Instruments',

    8

          home: HomePage(),

    9

        );

    10

      }

    11

    }

    12

    13

    class HomePage extends StatefulWidget {

    14

      const HomePage({super.key});

    15

    16

      @override

    17

      State<HomePage> createState() => _HomePageState();

    18

    }

    19

    20

    class _HomePageState extends State<HomePage> {

    21

      final _future = Supabase.instance.client

    22

          .from('instruments')

    23

          .select();

    24

    25

      @override

    26

      Widget build(BuildContext context) {

    27

        return Scaffold(

    28

          body: FutureBuilder(

    29

            future: _future,

    30

            builder: (context, snapshot) {

    31

              if (!snapshot.hasData) {

    32

                return const Center(child: CircularProgressIndicator());

    33

              }

    34

              final instruments = snapshot.data!;

    35

              return ListView.builder(

    36

                itemCount: instruments.length,

    37

                itemBuilder: ((context, index) {

    38

                  final instrument = instruments[index];

    39

                  return ListTile(

    40

                    title: Text(instrument['name']),

    41

                  );

    42

                }),

    43

              );

    44

            },

    45

          ),

    46

        );

    47

      }

    48

    }

6

### Start the app

Run your app on a platform of your choosing! By default an app should launch in your web browser.

Note that `supabase_flutter` is compatible with web, iOS, Android, macOS, and Windows apps. Running the app on macOS requires additional configuration to [set the entitlements](https://docs.flutter.dev/development/platform-integration/macos/building#setting-up-entitlements).

###### Terminal


    1

    flutter run

## Setup deep links#

Many sign in methods require deep links to redirect the user back to your app after authentication. Read more about setting deep links up for all platforms (including web) in the [Flutter Mobile Guide](/docs/guides/getting-started/tutorials/with-flutter#setup-deep-links).

## Going to production#

### Android#

In production, your Android app needs explicit permission to use the internet connection on the user's device which is required to communicate with Supabase APIs. To do this, add the following line to the `android/app/src/main/AndroidManifest.xml` file.


    1

    <manifest xmlns:android="http://schemas.android.com/apk/res/android">

    2

      <!-- Required to fetch data from the internet. -->

    3

      <uses-permission android:name="android.permission.INTERNET" />

    4

      <!-- ... -->

    5

    </manifest>

### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/getting-started/quickstarts/flutter so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/getting-started/quickstarts/flutter so I can ask questions about its contents)