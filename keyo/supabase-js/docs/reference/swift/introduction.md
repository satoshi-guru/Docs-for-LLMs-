---
title: "swiftClient Library"
url: https://supabase.com/docs/reference/swift/introduction
---

# swiftClient Library

Swift Reference v2.0

# swift Client Library

supabase-swift[View on GitHub](https://github.com/supabase/supabase-swift)

This reference documents every object and method available in Supabase's Swift library, [supabase-swift](https://github.com/supabase/supabase-swift). You can use supabase-swift to interact with your Postgres database, listen to database changes, invoke Deno Edge Functions, build login and user management functionality, and manage large files.

* * *

## Installing

### Install using Swift Package Manager#

You can install Supabase package using Swift Package Manager.

The package exposes multiple libraries, you can choose between adding all of them using Supabase, or some of:

  * `Auth`
  * `Realtime`
  * `Postgrest`
  * `Functions`
  * `Storage`


If you use Xcode, follow [Apple's dependencies guide](https://developer.apple.com/documentation/swift_packages/adding_package_dependencies_to_your_app) to add supabase-swift to your project. Use <https://github.com/supabase-community/supabase-swift.git> for the url when Xcode asks.

If you don't want the full Supabase environment, you can add individual packages, such as Functions, `Auth`, `Realtime`, `Storage`, or `PostgREST`.

Package.swift


    1

    let package = Package(

    2

        ...

    3

        dependencies: [

    4

            ...

    5

            .package(

    6

                url: "https://github.com/supabase/supabase-swift.git",

    7

                from: "2.0.0"

    8

            ),

    9

        ],

    10

        targets: [

    11

            .target(

    12

                name: "YourTargetName",

    13

                dependencies: [

    14

                    .product(

    15

                        name: "Supabase", // Auth, Realtime, Postgrest, Functions, or Storage

    16

                        package: "supabase-swift"

    17

                    ),

    18

                ]

    19

            )

    20

        ]

    21

    )

### Enable Data API access#

supabase-swift uses the Data API to query and mutate your Postgres data. You first need to grant Data API roles permissions to access your tables and functions.

In [Data API integrations settings](/dashboard/project/_/integrations/data_api/settings), expose the specific tables and functions you want to access. To automatically grant access for new tables and functions in `public`, enable **Default privileges for new entities**.

Alternatively, use SQL to grant the required permissions:


    1

    -- Before granting access to client roles, make sure RLS is enabled

    2

    -- and create the policies required for each role's allowed operations.

    3

    alter table public.your_table enable row level security;

    4

    -- create policy ... on public.your_table ...;

    5

    6

    -- Grant least-privilege access to tables after RLS and policies are in place

    7

    grant select on public.your_table to anon;

    8

    grant select, insert, update, delete on public.your_table to authenticated;

    9

    grant all on public.your_table to service_role;

    10

    11

    -- Grant execute on functions after verifying any table access they rely on

    12

    grant execute on function public.your_function to authenticated, service_role;

* * *

## Initializing

You can initialize Supabase with the `SupabaseClient` by passing your `Project URL` and `Project Key`. You can find these under your `Project Settings` → `API Settings` The Supabase client is your entrypoint to the rest of the Supabase functionality and is the easiest way to interact with everything we offer within the Supabase ecosystem.

Initialize ClientInitialize Client with custom optionsInitialize Client with automatic retriesInitialize Client with LoggingWith custom schemas


    1

    import Supabase

    2

    3

    let client = SupabaseClient(supabaseURL: URL(string: "https://xyzcompany.supabase.co")!, supabaseKey: "your-publishable-key")

* * *

## Fetch data

  * By default, Supabase projects will return a maximum of 1,000 rows. This setting can be changed in Project API Settings. It's recommended that you keep it low to limit the payload size of accidental or malicious requests. You can use `range()` queries to paginate through your data.
  * `select()` can be combined with [Modifiers](/docs/reference/swift/using-modifiers)
  * `select()` can be combined with [Filters](/docs/reference/swift/using-filters)
  * If using the Supabase hosted platform `apikey` is technically a reserved keyword, since the API gateway will pluck it out for authentication. [It should be avoided as a column name](https://github.com/supabase/supabase/issues/5465).
  * The recommended solution for getting data is to use the value property which will return a decoded model. Create a `Codable` to easily decode your database responses.


Getting your dataSelecting specific columnsQuery foreign tablesQuery foreign tables through a join tableQuery the same foreign table multiple timesFiltering through foreign tablesQuerying foreign table with countQuerying with count optionQuerying JSON dataQuerying foreign table with inner joinSwitching schemas per query


    1

    struct Instrument: Decodable {

    2

      let id: Int

    3

      let name: String

    4

    }

    5

    6

    let instruments: [Instrument] = try await supabase

    7

      .from("instruments")

    8

      .select()

    9

      .execute()

    10

      .value

Data source

Response

* * *

## Insert data

Create a recordCreate a record and return itBulk create


    1

    struct Instrument: Encodable {

    2

      let id: Int

    3

      let name: String

    4

    }

    5

    6

    let instrument = Instrument(id: 1, name: "ukelele")

    7

    8

    try await supabase

    9

      .from("instruments")

    10

      .insert(instrument)

    11

      .execute()

Data source

Response

* * *

## Update data

  * `update()` should always be combined with [Filters](/docs/reference/swift/using-filters) to target the item(s) you wish to update.


Updating your dataUpdate a record and return itUpdating JSON data


    1

    try await supabase

    2

      .from("instruments")

    3

      .update(["name": "piano"])

    4

      .eq("id", value: 1)

    5

      .execute()

Data source

Response

* * *

## Upsert data

  * Primary keys must be included in `values` to use upsert.


Upsert your dataBulk Upsert your dataUpserting into tables with constraints


    1

    struct Instrument: Encodable {

    2

      let id: Int

    3

      let name: String

    4

    }

    5

    try await supabase

    6

      .from("instruments")

    7

      .upsert(Instrument(id: 1, name: "piano"))

    8

      .execute()

Data source

Response

* * *

## Delete data

  * `delete()` should always be combined with [filters](/docs/reference/swift/using-filters) to target the item(s) you wish to delete.
  * If you use `delete()` with filters and you have [RLS](/docs/learn/auth-deep-dive/auth-row-level-security) enabled, only rows visible through `SELECT` policies are deleted. Note that by default no rows are visible, so you need at least one `SELECT`/`ALL` policy that makes the rows visible.


Delete records


    1

    try await supabase

    2

      .from("instruments")

    3

      .delete()

    4

      .eq("id", value: 1)

    5

      .execute()

Data source

Response

* * *

## Call a Postgres function

You can call Postgres functions as _Remote Procedure Calls_ , logic in your database that you can execute from anywhere. Functions are useful when the logic rarely changes—like for password resets and updates.


    1

    create or replace function hello_world() returns text as $$

    2

      select 'Hello world';

    3

    $$ language sql;

Call a Postgres function without argumentsCall a Postgres function with argumentsBulk processingCall a Postgres function with filters


    1

    let value: String = try await supabase

    2

      .rpc("hello_world")

    3

      .execute()

    4

      .value

Data source

Response

* * *

## Using filters

Filters allow you to only return rows that match certain conditions.

Filters can be used on `select()`, `update()`, `upsert()`, and `delete()` queries.

If a Postgres function returns a table response, you can also apply filters.

Implement `URLQueryRepresentable` protocol in your own types to be able to use them as filter value.

Supported filtes are: `eq`, `neq`, `gt`, `gte`, `lt`, `lte`, `like`, `ilike`, `is`, `in`, `cs`, `cd`, `sl`, `sr`, `nxl`, `nxr`, `adj`, `ov`, `fts`, `plfts`, `phfts`, `wfts`. Check available operators in [PostgREST](https://postgrest.org/en/stable/references/api/tables_views.html#operators).

Applying FiltersChainingConditional ChainingFilter by values within a JSON columnFilter Foreign Tables


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, country_id")

    4

      .eq("name", value: "The Shire")    // Correct

    5

    6

    try await supabase

    7

      .from("citites")

    8

      .eq("name", value: "The Shire")    // Incorrect

    9

      .select("name, country_id")

Notes

* * *

## Column is equal to a value

Match only rows where `column` is equal to `value`.

With `select()`


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, country_id")

    4

      .eq("name", value: "The shire")

Response

* * *

## Column is not equal to a value

Match only rows where `column` is not equal to `value`.

With `select()`


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, country_id")

    4

      .neq("name", value: "Paris")

* * *

## Column is greater than a value

Match only rows where `column` is greater than `value`.

With `select()`


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, country_id")

    4

      .gt("country_id", value: 250)

* * *

## Column is greater than or equal to a value

Match only rows where `column` is greater than or equal to `value`.

With `select()`


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, country_id")

    4

      .gte("country_id", value: 250)

* * *

## Column is less than a value

Match only rows where `column` is less than `value`.

With `select()`


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, country_id")

    4

      .lt("country_id", value: 250)

* * *

## Column is less than or equal to a value

Match only rows where `column` is less than or equal to `value`.

With `select()`


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, country_id")

    4

      .lte("country_id", value: 250)

* * *

## Column matches a pattern

Match only rows where `column` matches `pattern` case-sensitively.

With `select()`


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, country_id")

    4

      .like("name", pattern: "%la%")

* * *

## Column matches a case-insensitive pattern

Match only rows where `column` matches `pattern` case-insensitively.

With `select()`


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, country_id")

    4

      .ilike("name", pattern: "%la%")

* * *

## Column is a value

Match only rows where `column` IS `value`. For non-null values, this is equivalent to the `eq` filter. For null values, use this instead of `eq`.

With `select()`


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, country_id")

    4

      .is("name", value: nil)

* * *

## Column is in an array

Match only rows where `column` is included in the `values` array.

With `select()`


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, country_id")

    4

      .in("name", values: ["Rio de Janeiro", "San Francisco"])

* * *

## Column contains every element in a value

Match only rows where `column` contains every element appearing in `value`.

With `select()`


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, main_exports")

    4

      .contains("main_exports", value: ["oil"])

* * *

## With a common element

Match only rows where `column` and `value` have an element in common.

With `select()`


    1

    try await supabase

    2

      .from("cities")

    3

      .select("name, main_exports")

    4

      .overlaps("main_exports", value: ["exports", "tourism"])

* * *

## Match an associated value

With `select()`


    1

    try await supabase

    2

      .from("instruments")

    3

      .select("name")

    4

      .match(["id": 2, "name": "viola"])

Data source

Response

* * *

## Don't match the filter

Finds all rows that don't satisfy the filter.

  * `.not()` expects you to use the raw [PostgREST syntax](https://postgrest.org/en/stable/api.html#horizontal-filtering-rows) for the filter names and values.

        1

        .not("name", operator: .eq, value: "violin")

        2

        .not("arraycol", operator: .cs, value: #"{"a","b"}"#) // Use Postgres array {} for array column and 'cs' for contains.

        3

        .not("rangecol", operator: .cs, value: "(1,2]") // Use Postgres range syntax for range column.

        4

        .not("id", operator: .in, value: "(6,7)")  // Use Postgres list () and 'in' for in_ filter.

        5

        .not("id", operator: .in, value: "(\(mylist.join(separator: ",")))")  // You can insert a Swift list array.


With `select()`


    1

    try await supabase

    2

      .from("instruments")

    3

      .select()

    4

      .not("name", operator: .is, value: "")

    5

      .execute()

* * *

## Match at least one filter

or() expects you to use the raw PostgREST syntax for the filter names and values.


    1

    .or(#"id.in.(5,6,7), arraycol.cs.{"a","b"}"#)  // Use `()` for `in` filter, `{}` for array values and `cs` for `contains()`.

    2

    .or(#"id.in.(5,6,7), arraycol.cd.{"a","b"}"#)  // Use `cd` for `containedBy()`

With `select()`Use `or` with `and`


    1

    try await supabase

    2

      .from("instruments")

    3

      .select("name")

    4

      .or("id.eq.2,name.eq.cello")

Data source

Response

* * *

## Match the filter

filter() expects you to use the raw PostgREST syntax for the filter values.


    1

    .filter("id", operator: "in", value: "(5,6,7)")  // Use `()` for `in` filter

    2

    .filter("arraycol", operator: "cs", value: #"{"a","b"}"#)  // Use `cs` for `contains()`, `{}` for array values

With `select()`On a foreign table


    1

    try await supabase

    2

      .from("instruments")

    3

      .select()

    4

      .filter("name", operator: "in", value: #"("cello","guzheng")"#)

Data source

Response

* * *

## Using modifiers

Filters work on the row level—they allow you to return rows that only match certain conditions without changing the shape of the rows. Modifiers are everything that don't fit that definition—allowing you to change the format of the response (e.g. returning a CSV string).

Modifiers must be specified after filters. Some modifiers only apply for queries that return rows (e.g., `select()` or `rpc()` on a function that returns a table response).

* * *

## Return data after inserting

Perform a SELECT on the query result.

With `upsert()`


    1

    try await supabase

    2

        .from("instruments")

    3

        .upsert(InstrumentModel(id: 1, name: "piano"))

    4

        .select()

    5

        .execute()

Data source

Response

* * *

## Order the results

Order the query result by column.

With `select()`On a foreign tableOrder parent table by a referenced table


    1

    try await supabase

    2

      .from("instruments")

    3

      .select("id, name")

    4

      .order("id", ascending: false)

    5

      .execute()

Data source

Response

* * *

## Limit the number of rows returned

Limit the query result by count.

With `select()`On a foreign table


    1

    try await supabase

    2

      .from("instruments")

    3

      .select("id, name")

    4

      .limit(1)

    5

      .execute()

Data source

Response

* * *

## Limit the query to a range

Limit the query result by from and to inclusively.

With `select()`


    1

    try await supabase

    2

      .from("instruments")

    3

      .select("name")

    4

      .range(from: 0, to: 1)

    5

      .execute()

Data source

Response

* * *

## Retrieve one row of data

By default PostgREST returns all JSON results in an array, even when there is only one item, use `single()` to return the first object unenclosed by an array.

With `select()`


    1

    try await supabase

    2

      .from("instruments")

    3

      .select("name")

    4

      .limit(1)

    5

      .single()

    6

      .execute()

Data source

Response

* * *

## Retrieve as a CSV

Return data as CSV


    1

    try await supabase

    2

      .from("instruments")

    3

      .select()

    4

      .csv()

    5

      .execute()

Data source

Response

Notes

* * *

## Strip null values

Strip null values from the response.

  * Requires PostgREST 11.2.0 or later.
  * Cannot be combined with `.csv()`.


Strip null values


    1

    let data = try await supabase

    2

      .from("characters")

    3

      .select()

    4

      .stripNulls()

    5

      .execute()

    6

      .value

* * *

## Using explain

For debugging slow queries, you can get the [Postgres `EXPLAIN` execution plan](https://www.postgresql.org/docs/current/sql-explain.html) of a query using the `explain()` method. This works on any query, even for `rpc()` or writes.

Explain is not enabled by default as it can reveal sensitive information about your database. It's best to only enable this for testing environments but if you wish to enable it for production you can provide additional protection by using a `pre-request` function.

Follow the [Performance Debugging Guide](/docs/guides/database/debugging-performance) to enable the functionality on your project.

Get the execution planGet the execution plan with analyze and verbose


    1

    try await supabase

    2

      .from("instruments")

    3

      .select()

    4

      .explain()

    5

      .execute()

    6

      .value

Data source

Response

Notes

* * *

## Overview

The auth methods can be accessed via the `supabase.auth` namespace.

### Handling deep links#

#### UIKit app lifecycle#


    1

    public func application(

    2

      _ application: UIApplication,

    3

      didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?

    4

    ) -> Bool {

    5

      if let url = launchOptions?[.url] as? URL {

    6

        supabase.auth.handle(url)

    7

      }

    8

    9

      return true

    10

    }

    11

    12

    func application(

    13

      _ app: UIApplication,

    14

      open url: URL,

    15

      options: [UIApplication.OpenURLOptionsKey: Any]

    16

    ) -> Bool {

    17

      supabase.auth.handle(url)

    18

      return true

    19

    }

    20

    21

    #### UIKit app lifecycle with scenes

    22

    23

    In your `SceneDelegate.swift`:

    24

    25

    ```swift

    26

    func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {

    27

      guard let url = URLContexts.first?.url else { return }

    28

      supabase.auth.handle(url)

    29

    }

#### SwiftUI app lifecycle#

In your `AppDelegate.swift`:


    1

    SomeView()

    2

      .onOpenURL { url in

    3

        supabase.auth.handle(url)

    4

      }

Create auth clientCreate auth client with custom storage


    1

    let supabase = SupabaseClient(supabaseURL: URL(string: "https://xyzcompany.supabase.co")!, supabaseKey: "your-publishable-key")

    2

    let auth = supabase.auth

* * *

## Create a new user

  * By default, the user needs to verify their email address before logging in. To turn this off, disable **Confirm email** in [your project](/dashboard/project/_/auth/providers).
  * **Confirm email** determines if users need to confirm their email address after signing up.
    * If **Confirm email** is enabled, a `user` is returned but `session` is null.
    * If **Confirm email** is disabled, both a `user` and a `session` are returned.
  * When the user confirms their email address, they are redirected to the [`SITE_URL`](/docs/guides/auth/redirect-urls) by default. You can modify your `SITE_URL` or add additional redirect URLs in [your project](/dashboard/project/_/auth/url-configuration).
  * If signUp() is called for an existing confirmed user:
    * When both **Confirm email** and **Confirm phone** (even when phone provider is disabled) are enabled in [your project](/dashboard/project/_/auth/providers), an obfuscated/fake user object is returned.
    * When either **Confirm email** or **Confirm phone** (even when phone provider is disabled) is disabled, the error message, `User already registered` is returned.
  * To fetch the currently logged-in user, refer to [`getUser()`](/docs/reference/swift/get-user).


### Parameters

  * email

Optional

String

One of `email` or `phone` must be provided.

  * phone

Optional

String

One of `email` or `phone` must be provided.

  * passwordString

  * data

Optional

JSONObject

A custom data object to store additional user metadata.

  * redirectTo

Optional

URL

Only for email signups. The redirect URL embedded in the email link. Must be a configured redirect URL for your Supabase instance.

  * captchaToken

Optional

String


Sign up with email and passwordSign up with a phone number and password (SMS)Sign up with a phone number and password (whatsapp)Sign up with additional user metadataSign up with a redirect URL


    1

    try await supabase.auth.signUp(

    2

      email: "example@email.com",

    3

      password: "example-password"

    4

    )

* * *

## Listen to auth events

  * Subscribes to important events occurring on the user's session.
  * Emitted events:
    * `INITIAL_SESSION`
      * Emitted right after the Supabase client is constructed and the initial session from storage is loaded.
    * `SIGNED_IN`
      * Emitted each time a user session is confirmed or re-established, including on user sign in.
      * Avoid making assumptions as to when this event is fired, this may occur even when the user is already signed in. Instead, check the user object attached to the event to see if a new user has signed in and update your application's UI.
    * `SIGNED_OUT`
      * Emitted when the user signs out. This can be after:
        * A call to `supabase.auth.signOut()`.
        * After the user's session has expired for any reason:
          * User has signed out on another device.
          * The session has reached its timebox limit or inactivity timeout.
          * User has signed in on another device with single session per user enabled.
          * Check the [User Sessions](/docs/guides/auth/sessions) docs for more information.
      * Use this to clean up any local storage your application has associated with the user.
    * `TOKEN_REFRESHED`
      * Emitted each time a new access and refresh token are fetched for the signed in user.
      * It's best practice and highly recommended to extract the access token (JWT) and store it in memory for further use in your application.
        * Avoid frequent calls to `supabase.auth.session` for the same purpose.
      * There is a background process that keeps track of when the session should be refreshed so you will always receive valid tokens by listening to this event.
      * The frequency of this event is related to the JWT expiry limit configured on your project.
    * `USER_UPDATED`
      * Emitted each time the `supabase.auth.update(user:)` method finishes successfully. Listen to it to update your application's UI based on new profile information.
    * `PASSWORD_RECOVERY`
      * Emitted instead of the `SIGNED_IN` event when the user lands on a page that includes a password recovery link in the URL.
      * Use it to show a UI to the user where they can [reset their password](/docs/guides/auth/passwords#resetting-a-users-password-forgot-password).


Listen to auth changesListen to a specific event


    1

    // Using AsyncStream

    2

    for await (event, session) in await supabase.auth.authStateChanges {

    3

      print(event, session)

    4

    }

    5

    6

    // Using Closure

    7

    let subscription = await supabase.auth.onAuthStateChange { event, session in

    8

      print(event, session)

    9

    }

    10

    11

    // call remove() to remove subscription

    12

    subscription.remove()

Notes

* * *

## Create an anonymous user

  * Returns an anonymous user
  * It is recommended to set up captcha for anonymous sign-ins to prevent abuse. You can pass in the captcha token in the `options` param.


Create an anonymous userCreate an anonymous user with custom user metadata


    1

    let session = try await supabase.auth.signInAnonymously(captchaToken: captchaToken)

* * *

## Sign in a user

  * Requires either an email and password or a phone number and password.


### Parameters

  * email

Optional

String

One of `email` or `phone` must be provided.

  * phone

Optional

String

One of `email` or `phone` must be provided.

  * passwordString

  * captchaToken

Optional

String


Sign in with email and passwordSign in with phone and password


    1

    try await supabase.auth.signIn(

    2

      email: "example@email.com",

    3

      password: "example-password"

    4

    )

* * *

## Sign in with ID token (native sign-in)

Sign In using ID Token


    1

    let session = try await supabase.auth.signInWithIdToken(

    2

      credentials: OpenIDConnectCredentials(

    3

        provider: .apple,

    4

        idToken: "your-id-token",

    5

        nonce: "your nonce"

    6

      )

    7

    )

Notes

* * *

## Sign in a user through OTP

  * Requires either an email or phone number.
  * This method is used for passwordless sign-ins where a OTP is sent to the user's email or phone number.
  * If the user doesn't exist, `signInWithOTP()` will signup the user instead. To restrict this behavior, you can set `shouldCreateUser` to `false``.
  * If you're using an email, you can configure whether you want the user to receive a magiclink or a OTP.
  * If you're using phone, you can configure whether you want the user to receive a OTP.
  * The magic link's destination URL is determined by the [`SITE_URL`](/docs/guides/auth/redirect-urls).
  * See [redirect URLs and wildcards](/docs/guides/auth#redirect-urls-and-wildcards) to add additional redirect URLs to your project.
  * Magic links and OTPs share the same implementation. To send users a one-time code instead of a magic link, [modify the magic link email template](/dashboard/project/_/auth/templates) to include `{{ .Token }}` instead of `{{ .ConfirmationURL }}`.
  * See our [Twilio Phone Auth Guide](/docs/guides/auth/phone-login/twilio) for details about configuring WhatsApp sign in.


### Parameters

  * email

Optional

String

One of `email` or `phone` must be provided.

  * phone

Optional

String

One of `email` or `phone` must be provided.

  * redirectTo

Optional

String

Only for email signups. The redirect URL embedded in the email link. Must be a configured redirect URL for your Supabase instance.

  * channel

Optional

MessagingChannel

The channel to use for sending messages. Only for phone signups.

  * shouldCreateUser

Optional

Bool

Whether to create the user if they don't already exist. Defaults to true.

  * data

Optional

JSONObject

A custom data object to store additional user metadata.

  * captchaToken

Optional

String


Sign in with emailSign in with SMS OTPSign in with WhatsApp OTP


    1

    try await supabase.auth.signInWithOTP(

    2

      email: "example@email.com",

    3

      redirectTo: URL(string: "my-app-scheme://")!

    4

    )

Notes

* * *

## Sign in a user through OAuth

  * This method is used for signing in using a third-party provider.
  * Supabase supports many different [third-party providers](https://supabase.com/docs/guides/auth#providers).


### Parameters

  * providerProvider

The third-party provider.

  * redirectTo

Optional

URL

A URL to send the user to after they are confirmed.

  * scopes

Optional

String

A space-separated list of scopes granted to the OAuth application.

  * queryParams

Optional

[(name: String, value: String?)]

Additional query params.

  * configure

Optional

Callback

A custom configuration callback for opening the OAuth flow externally.


Sign in with OAuth using ASWebAuthenticationSessionSign in with OAuth and customize flowSign in using a third-party providerSign in with scopes


    1

    let session = try await supabase.auth.signInWithOAuth(

    2

      provider: .github

    3

    ) { (session: ASWebAuthenticationSession) in

    4

      // customize session

    5

    }

Notes

* * *

## Sign in a user through SSO

  * Before you can call this method you need to [establish a connection](/docs/guides/auth/sso/auth-sso-saml#managing-saml-20-connections) to an identity provider. Use the [CLI commands](/docs/reference/cli/supabase-sso) to do this.
  * If you've associated an email domain to the identity provider, you can use the `domain` property to start a sign-in flow.
  * In case you need to use a different way to start the authentication flow with an identity provider, you can use the `providerId` property. For example:
    * Mapping specific user email addresses with an identity provider.
    * Using different hints to identity the identity provider to be used by the user, like a company-specific page, IP address or other tracking information.


### Parameters

  * providerId

Optional

String

UUID of the SSO provider. One of `providerId` or `domain` is required.

  * domain

Optional

String

Domain name of the organization to use SSO with. One of `providerId` or `domain` is required.

  * redirectToString

The URL to redirect the user to after they have signed in. Must be a configured redirect URL for your Supabase instance.

  * captchaTokenString


Sign in with email domainSign in with provider UUID


    1

    // You can extract the user's email domain and use it to trigger the

    2

      // authentication flow with the correct identity provider.

    3

    4

      let url = try await await supabase.auth.signInWithSSO{

    5

        domain: "company.com"

    6

      }

    7

    8

      // Open the URL using your preferred method to complete sign-in process.

    9

      UIApplication.shared.open(url)

* * *

## Get user claims from verified JWT

  * Verifies a JWT and extracts its claims.
  * For symmetric JWTs (HS256), verification is performed server-side via the `getUser()` API.
  * For asymmetric JWTs (RS256), verification is performed client-side using Apple Security framework.
  * Uses a global JWKS cache shared across all clients with the same storage key for optimal performance.
  * Automatically handles key rotation by falling back to server-side verification when a JWK is not found.
  * The JWKS cache has a 10-minute TTL (time-to-live).


### Parameters

  * jwt

Optional

String

The JWT to verify. If not provided, uses the access token from the current session.

  * options

Optional

GetClaimsOptions

Options for JWT verification. Can specify `allowExpired` to skip expiration check and `jwks` to provide custom JSON Web Key Set.


Verify and get claims from current sessionVerify and get claims from a specific JWTGet claims from an expired JWTVerify JWT with custom JWKS


    1

    let response = try await supabase.auth.getClaims()

    2

    print("User ID: \(response.claims.sub ?? "N/A")")

    3

    print("Email: \(response.claims.email ?? "N/A")")

    4

    print("Role: \(response.claims.role ?? "N/A")")

* * *

## Sign out a user

  * In order to use the `signOut()` method, the user needs to be signed in first.


Sign out


    1

    try await supabase.auth.signOut()

* * *

## Verify and log in through OTP

  * The `verifyOTP` method takes in different verification types. If a phone number is used, the type can either be `sms` or `phone_change`. If an email address is used, the type can be one of the following: `signup`, `magiclink`, `recovery`, `invite`, `email_change`, or `email`.
  * The verification type used should be determined based on the corresponding auth method called before `verifyOTP` to sign up / sign-in a user.


### Parameters

  * email

Optional

String

One of `phone`, `email`, or `token_hash` must be provided.

  * phone

Optional

String

One of `phone`, `email`, or `token_hash` must be provided.

  * token_hash

Optional

String

The token hash from the user's email link. One of `phone`, `email`, or `token_hash` must be provided.

  * typeEmailOTPType | MobileOTPType

  * token

Optional

String

The OTP sent to the user. Required if using `phone` or `email`.

  * redirectTo

Optional

URL

A URL to redirect the user to after they are confirmed. Must be in your configured redirect URLs.

  * captchaToken

Optional

String

Deprecated.


Verify Sms One-Time Password (OTP)Verify Signup One-Time Password (OTP)


    1

    try await supabase.auth.verifyOTP(

    2

      phone: "+13334445555",

    3

      token: "123456",

    4

      type: .sms

    5

    )

* * *

## Retrieve a session

  * Returns the session, refreshing it if necessary. If no session can be found, a `GoTrueError.sessionNotFound` error is thrown.


Get the session dataGet the current session without validation


    1

    try await supabase.auth.session

* * *

## Retrieve a new session

  * This method will refresh the session whether the current one is expired or not.


Refresh session using the current sessionRefresh session using a refresh token


    1

    let session = try await supabase.auth.refreshSession()

* * *

## Retrieve a user

  * This method is useful for checking if the user is authorized because it validates the user's access token JWT on the server.
  * Fetches the user object from the database instead of local session.
  * Should be used only when you require the most current user data. For faster results, `session.user` is recommended.


Get the logged in user with the current existing sessionGet the logged in user with a custom access token jwtGet current user


    1

    let user = try await supabase.auth.user()

* * *

## Update a user

  * In order to use the `updateUser()` method, the user needs to be signed in first.
  * By default, email updates sends a confirmation link to both the user's current and new email. To only send a confirmation link to the user's new email, disable **Secure email change** in your project's [email auth provider settings](https://supabase.com/dashboard/project/_/auth/providers).


Update the email for an authenticated userUpdate the phone number for an authenticated userUpdate the password for an authenticated userUpdate the user's metadataUpdate the user's password with a nonce


    1

    try await supabase.auth.update(user: UserAttributes(email: "new@email.com"))

Response

Notes

* * *

## Retrieve identities linked to a user

  * The user needs to be signed in to call `userIdentities()`.


Returns a list of identities linked to the user


    1

    let identities = try await supabase.auth.userIdentities()

* * *

## Link an identity to a user

  * The **Enable Manual Linking** option must be enabled from your [project's authentication settings](/dashboard/project/_/auth/providers).
  * The user needs to be signed in to call `linkIdentity()`.
  * If the candidate identity is already linked to the existing user or another user, `linkIdentity()` will fail.


Link an identity to a userLink an identity to a user with custom URL opening logic


    1

    try await supabase.auth.linkIdentity(provider: provider)

Notes

* * *

## Unlink an identity from a user

  * The **Enable Manual Linking** option must be enabled from your [project's authentication settings](/dashboard/project/_/auth/providers).
  * The user needs to be signed in to call `unlinkIdentity()`.
  * The user must have at least 2 identities in order to unlink an identity.
  * The identity to be unlinked must belong to the user.


Unlink an identity


    1

    // retrieve all identities linked to a user

    2

    let identities = try await supabase.auth.userIdentities()

    3

    4

    // find the google identity

    5

    let googleIdentity = identities.first {

    6

      $0.provider == .google

    7

    }

    8

    9

    // unlink the google identity

    10

    try await supabase.auth.unlinkIdentity(googleIdentity)

* * *

## Send a password reauthentication nonce

  * This method is used together with `update(user:)` when a user's password needs to be updated.
  * If you require your user to reauthenticate before updating their password, you need to enable the **Secure password change** option in your [project's email provider settings](/dashboard/project/_/auth/providers).
  * A user is only require to reauthenticate before updating their password if **Secure password change** is enabled and the user **hasn't recently signed in**. A user is deemed recently signed in if the session was created in the last 24 hours.
  * This method will send a nonce to the user's email. If the user doesn't have a confirmed email address, the method will send the nonce to the user's confirmed phone number instead.


Send reauthentication nonce


    1

    try await supabase.auth.reauthenticate()

Notes

* * *

## Resend an OTP

  * Resends a signup confirmation, email change, or phone change email to the user.
  * Passwordless sign-ins can be resent by calling the `signInWithOTP()` method again.
  * Password recovery emails can be resent by calling the `resetPasswordForEmail()` method again.
  * This method only resends an email or phone OTP to the user if there an initial signup, email change, or phone change request was made.
  * You can specify a redirect URL when you resend an email link using the `emailRedirectTo` option.


Resend an email signup confirmationResend a phone signup confirmationResend email change emailResend phone change OTP


    1

    try await supabase.auth.resend(

    2

      email: "email@example.com",

    3

      type: .signup,

    4

      emailRedirectTo: URL(string: "my-app-scheme://")

    5

    )

Notes

* * *

## Set the session data

  * `setSession()` takes in a refresh token and uses it to get a new session.
  * The refresh token can only be used once to obtain a new session.
  * [Refresh token rotation](/docs/reference/auth/config#refresh_token_rotation_enabled) is enabled by default on all projects to guard against replay attacks.
  * You can configure the [`REFRESH_TOKEN_REUSE_INTERVAL`](https://supabase.com/docs/reference/auth/config#refresh_token_reuse_interval) which provides a short window in which the same refresh token can be used multiple times in the event of concurrency or offline issues.


Refresh the session


    1

    try await supabase.auth.setSession(accessToken: "access_token", refreshToken: "refresh_token")

Notes

* * *

## Exchange an auth code for a session

  * Used when `flowType` is set to `pkce` in client options.


Exchange Auth Code


    1

    try await supabase.auth.exchangeCodeForSession(authCode: "34e770dd-9ff9-416c-87fa-43b31d7ef225")

* * *

## Start auto-refresh session (non-browser)

Starts the automatic session refresh process.

Start automatic session refresh


    1

    supabase.auth.startAutoRefresh()

* * *

## Stop auto-refresh session (non-browser)

Stops the automatic session refresh process.

Stop automatic session refresh


    1

    supabase.auth.stopAutoRefresh()

* * *

## Auth MFA

This section contains methods commonly used for Multi-Factor Authentication (MFA) and are invoked behind the `supabase.auth.mfa` namespace.

Currently, we only support time-based one-time password (TOTP) as the 2nd factor. We don't support recovery codes but we allow users to enroll more than 1 TOTP factor, with an upper limit of 10.

Having a 2nd TOTP factor for recovery frees the user of the burden of having to store their recovery codes somewhere. It also reduces the attack surface since multiple recovery codes are usually generated compared to just having 1 backup TOTP factor.

* * *

## Enroll a factor

  * Currently, `totp` is the only supported `factorType`. The returned `id` should be used to create a challenge.
  * To create a challenge, see [`mfa.challenge()`](/docs/reference/swift/auth-mfa-challenge).
  * To verify a challenge, see [`mfa.verify()`](/docs/reference/swift/auth-mfa-verify).
  * To create and verify a challenge in a single step, see [`mfa.challengeAndVerify()`](/docs/reference/swift/auth-mfa-challengeandverify).


Enroll a time-based, one-time password (TOTP) factor


    1

    let response = try await supabase.auth.mfa.enroll(

    2

      params: MFAEnrollParams(

    3

        issuer: "optional issuer",

    4

        friendlyName: "optional friendly name"

    5

      )

    6

    )

    7

    8

    // Use the id to create a challenge.

    9

    // The challenge can be verified by entering the code generated from the authenticator app.

    10

    // The code will be generated upon scanning the qrCode or entering the secret into the authenticator app.

    11

    let id = response.id

    12

    let type = response.type

    13

    let qrCode = response.totp?.qrCode

    14

    let secret = response.totp?.secret

    15

    let uri = response.totp?.uri

* * *

## Create a challenge

  * An [enrolled factor](/docs/reference/swift/auth-mfa-enroll) is required before creating a challenge.
  * To verify a challenge, see [`mfa.verify()`](/docs/reference/swift/auth-mfa-verify).


Create a challenge for a factor


    1

    let response = try await supabase.auth.mfa.challenge(

    2

      params: MFAChallengeParams(

    3

        factorId: "34e770dd-9ff9-416c-87fa-43b31d7ef225"

    4

      )

    5

    )

* * *

## Verify a challenge

  * To verify a challenge, please [create a challenge](/docs/reference/swift/auth-mfa-challenge) first.


Verify a challenge for a factor


    1

    let session = try await supabase.auth.mfa.verify(

    2

      params: MFAVerifyParams(

    3

        factorId: "34e770dd-9ff9-416c-87fa-43b31d7ef225",

    4

        challengeId: "4034ae6f-a8ce-4fb5-8ee5-69a5863a7c15",

    5

        code: "123456"

    6

      )

    7

    )

* * *

## Create and verify a challenge

  * An [enrolled factor](/docs/swift/javascript/auth-mfa-enroll) is required before invoking `challengeAndVerify()`.
  * Executes [`mfa.challenge()`](/docs/reference/swift/auth-mfa-challenge) and [`mfa.verify()`](/docs/reference/swift/auth-mfa-verify) in a single step.


Create and verify a challenge for a factor


    1

    let session = try await supabase.auth.mfa.challengeAndVerify(

    2

      params: MFAChallengeAndVerifyParams(

    3

        factorId: "34e770dd-9ff9-416c-87fa-43b31d7ef225",

    4

        code: "123456"

    5

      )

    6

    )

* * *

## Unenroll a factor

  * Since v2.41.1, the unenroll response uses `id` instead of `factorId` to match the server response format. If upgrading from an earlier version, update your code to use `response.id`.


Unenroll a factor


    1

    let response = try await supabase.auth.mfa.unenroll(

    2

      params: MFAUnenrollParams(

    3

        factorId: "34e770dd-9ff9-416c-87fa-43b31d7ef225"

    4

      )

    5

    )

    6

    print(response.id) // ID of the unenrolled factor

* * *

## Get Authenticator Assurance Level

  * Authenticator Assurance Level (AAL) is the measure of the strength of an authentication mechanism.
  * In Supabase, having an AAL of `aal1` refers to having the 1st factor of authentication such as an email and password or OAuth sign-in while `aal2` refers to the 2nd factor of authentication such as a time-based, one-time-password (TOTP).
  * If the user has a verified factor, the `nextLevel` field will return `aal2`, else, it will return `aal1`.


Get the AAL details of a session


    1

    let aal = try await supabase.auth.mfa.getAuthenticatorAssuranceLevel()

    2

    let currentLevel = aal.currentLevel

    3

    let nextLevel = aal.nextLevel

    4

    let currentAuthenticationMethods = aal.currentAuthenticationMethods

* * *

## Auth Admin

  * Any method under the `supabase.auth.admin` namespace requires a `secret` key.
  * These methods are considered admin methods and should be called on a trusted server. Never expose your `secret` key in the browser.


Create server-side auth client


    1

    import Supabase

    2

    3

    let supabase = SupabaseClient(

    4

      supabaseURL: supabaseURL,

    5

      supabaseKey: secretKey

    6

    )

    7

    8

    // Access auth admin api

    9

    let adminAuthClient = supabase.auth.admin

* * *

## Retrieve a user

Get user by ID.

  * The `getUserById()` method requires a user's ID.


Get user by ID


    1

    let user = try await supabase.auth.admin.getUserById(

    2

      "715ed5db-f090-4b8c-a067-640ecee36aa0"

    3

    )

* * *

## List all users

List all users in the system.

List usersList users with pagination


    1

    let users = try await supabase.auth.admin.listUsers()

* * *

## Create a user

Create a new user.

Create user


    1

    let user = try await supabase.auth.admin.createUser(

    2

      attributes: AdminUserAttributes(

    3

        email: "user@email.com",

    4

        password: "password",

    5

        emailConfirm: true

    6

      )

    7

    )

* * *

## Delete a user

  * The `deleteUser()` method requires the user's ID, which maps to the `auth.users.id` column.


Removes a user


    1

    try await supabase.auth.admin.deleteUser(

    2

      id: "715ed5db-f090-4b8c-a067-640ecee36aa0"

    3

    )

* * *

## Send an email invite link

Send an invite link to the user's email address.

Invite user by email


    1

    let user = try await supabase.auth.admin.inviteUserByEmail(

    2

      "user@email.com",

    3

      data: ["role": "admin"],

    4

      redirectTo: URL(string: "https://example.com/welcome")

    5

    )

* * *

## Update a user

Update user by ID.

Update user by ID


    1

    let user = try await supabase.auth.admin.updateUserById(

    2

      "715ed5db-f090-4b8c-a067-640ecee36aa0",

    3

      attributes: AdminUserAttributes(

    4

        email: "newemail@email.com"

    5

      )

    6

    )

* * *

## Invokes a Supabase Edge Function.

Invoke a Supabase Edge Function.

  * Requires an Authorization header.
  * When you pass in a body to your function, we automatically attach the Content-Type header for `String`, and `Data`. If it doesn't match any of these types we assume the payload is `json`, serialize it and attach the `Content-Type` header as `application/json`. You can override this behaviour by passing in a `Content-Type` header of your own.
  * When a region is specified, both the `x-region` header and `forceFunctionRegion` query parameter are set to ensure proper function routing.


Invocation with `Decodable` responseInvocation with custom responseInvocation with streamed responseError handlingPassing custom headersInvoking a Function in the UsEast1 regionCalling with DELETE HTTP verbCalling with GET HTTP verbPass additional query params


    1

    struct Response: Decodable {

    2

      // Expected response definition

    3

    }

    4

    5

    let response: Response = try await supabase.functions

    6

      .invoke(

    7

        "hello",

    8

        options: FunctionInvokeOptions(

    9

          body: ["foo": "bar"]

    10

        )

    11

      )

* * *

## Subscribe to channel

  * By default, Broadcast and Presence are enabled for all projects.
  * By default, listening to database changes is disabled for new projects due to database performance and security concerns. You can turn it on by managing Realtime's [replication](/docs/guides/api#realtime-api-overview).
  * You can receive the "previous" data for updates and deletes by setting the table's `REPLICA IDENTITY` to `FULL` (e.g., `ALTER TABLE your_table REPLICA IDENTITY FULL;`).
  * Row level security is not applied to delete statements. When RLS is enabled and replica identity is set to full, only the primary key is sent to clients.
  * Use AsyncStream or callbacks for listening to changes.
  * Presence and Postgres change callbacks must be registered **before** calling `subscribe()`. Attempting to add them after the channel is subscribing or subscribed will trigger a warning and the callback will be rejected.


Listen to broadcast messagesListen to broadcast messages using callbackConfigure broadcast with replayListen to presence updatesListen to presence updates using callbackListen to all database changesListen to all database changes using callbackListen to a specific tableListen to a specific table using callbackListen to insertsListen to inserts using callbackListen to updatesListen to updates using callbackListen to deletesListen to deletes using callbackListen to row level changesListen to row level changes using callback


    1

    let channel = supabase.channel("room1")

    2

    3

    let broadcastStream = channel.broadcastStream(event: "cursor-pos")

    4

    5

    await channel.subscribe()

    6

    7

    Task {

    8

      for await message in broadcastStream {

    9

        print("Cursor position received", message)

    10

      }

    11

    }

    12

    13

    await channel.broadcast(

    14

      event: "cursor-pos",

    15

      message: [

    16

        "x": .double(.random(in: 0...1)),

    17

        "y": .double(.random(in: 0...1))

    18

      ]

    19

    )

* * *

## Unsubscribe from a channel

Unsubscribes and removes Realtime channel from Realtime client.

  * Removing a channel is a great way to maintain the performance of your project's Realtime service as well as your database if you're listening to Postgres changes.
  * Supabase will automatically handle cleanup 30 seconds after a client is disconnected, but unused channels may cause degradation as more clients are simultaneously subscribed.
  * If you removed all channels, the client automatically disconnects from the Realtime websocket. This can be disabled in the Realtime config by setting `disconnectOnNoSubscriptions` to false.


Remove a channelUnsubscribe from a channel


    1

    let channel = supabase.channel("channelId")

    2

    3

    //...

    4

    await supabase.removeChannel(channel)

* * *

## Unsubscribe from all channels

Unsubscribes and removes all Realtime channels from Realtime client.

  * Removing channels is a great way to maintain the performance of your project's Realtime service as well as your database if you're listening to Postgres changes. Supabase will automatically handle cleanup 30 seconds after a client is disconnected, but unused channels may cause degradation as more clients are simultaneously subscribed.
  * If you removed all channels, the client automatically disconnects from the Realtime websocket. This can be disabled in the Realtime config by setting `disconnectOnNoSubscriptions` to false.


Remove all channels


    1

    await supabase.removeAllChannels()

* * *

## Retrieve all channels

Returns all Realtime channels.

Get all channels


    1

    let channels = supabase.channels

* * *

## File Buckets

This section contains methods for working with File Buckets.

* * *

## List all buckets

  * RLS policy permissions required:
    * `buckets` table permissions: `select`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


List buckets


    1

    try await supabase.storage

    2

      .listBuckets()

* * *

## Retrieve a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: `select`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Get bucket


    1

    let bucket = try await supabase.storage

    2

      .getBucket("avatars")

* * *

## Create a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: `insert`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Create bucket


    1

    try await supabase.storage

    2

      .createBucket(

    3

        "avatars",

    4

        options: BucketOptions(

    5

          public: false,

    6

          allowedMimeTypes: ["image/png"],

    7

          fileSizeLimit: 1024

    8

        )

    9

      )

* * *

## Empty a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: `select`
    * `objects` table permissions: `select` and `delete`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Empty bucket


    1

    try await supabase.storage

    2

      .emptyBucket("avatars")

* * *

## Update a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: `select` and `update`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Update bucket


    1

    try await supabase.storage

    2

      .updateBucket(

    3

        "avatars",

    4

        options: BucketOptions(

    5

          public: false,

    6

          fileSizeLimit: 1024,

    7

          allowedMimeTypes: ["image/png"]

    8

        )

    9

      )

* * *

## Delete a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: `select` and `delete`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Delete bucket


    1

    try await supabase.storage

    2

      .deleteBucket("avatars")

* * *

## Upload a file

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: only `insert` when you are uploading new files and `select`, `insert` and `update` when you are upserting files
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Upload file


    1

    let fileName = "avatar1.png"

    2

    3

    try await supabase.storage

    4

      .from("avatars")

    5

      .upload(

    6

        path: "public/\(fileName)",

    7

        file: fileData,

    8

        options: FileOptions(

    9

          cacheControl: "3600",

    10

          contentType: "image/png",

    11

          upsert: false

    12

        )

    13

      )

* * *

## Replace an existing file

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `update` and `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Update file


    1

    let fileName = "avatar1.png"

    2

    3

    try await supabase.storage

    4

      .from("avatars")

    5

      .update(

    6

        path: "public/\(fileName)",

    7

        file: fileData,

    8

        options: FileOptions(

    9

          cacheControl: "3600",

    10

          contentType: "image/png",

    11

          upsert: true

    12

        )

    13

      )

* * *

## Move an existing file

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `update` and `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Move file


    1

    try await supabase

    2

      .storage

    3

      .from("avatars")

    4

      .move(from: "public/avatar1.png", to: "private/avatar2.png")

* * *

## Copy an existing file

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `insert` and `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Copy file


    1

    try await supabase

    2

      .storage

    3

      .from("avatars")

    4

      .copy(from: "public/avatar1.png", to: "private/avatar2.png")

* * *

## Create a signed URL

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Create Signed URLCreate a signed URL for an asset with transformationsCreate a signed URL which triggers the download of the asset


    1

    let signedURL = try await supabase.storage

    2

      .from("avatars")

    3

      .createSignedURL(path: "folder/avatar1.png", expiresIn: 60)

* * *

## Create signed URLs

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Create Signed URLs


    1

    let urls = try await supabase

    2

      .storage

    3

      .from("avatars")

    4

      .createSignedURLs(paths: ["folder/avatar1.png", "folder/avatar2.png"], expiresIn: 60)

* * *

## Create signed upload URL

Create a signed upload URL that can be used to upload files to a bucket.

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `insert`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Create signed upload URLCreate signed upload URL with options


    1

    let signedUploadUrl = try await supabase.storage

    2

      .from("avatars")

    3

      .createSignedUploadURL(path: "folder/avatar1.png")

* * *

## Upload to a signed URL

Upload a file to a bucket using a signed URL.

  * Use this method to upload files using a signed upload URL created with `createSignedUploadURL()`.


Upload to signed URLUpload file from URL to signed URL


    1

    let fileData = "Hello World".data(using: .utf8)!

    2

    3

    try await supabase.storage

    4

      .from("avatars")

    5

      .uploadToSignedURL(

    6

        "folder/avatar1.png",

    7

        token: "your-signed-token",

    8

        data: fileData,

    9

        options: FileOptions(

    10

          contentType: "text/plain"

    11

        )

    12

      )

* * *

## Retrieve public URL

  * The bucket needs to be set to public, either via [updateBucket()](/docs/reference/javascript/storage-updatebucket) or by going to Storage on [supabase.com/dashboard](https://supabase.com/dashboard), clicking the overflow menu on a bucket and choosing "Make public"
  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Returns the URL for an asset in a public bucketReturns the URL for an asset in a public bucket with transformationsReturns the URL which triggers the download of an asset in a public bucket


    1

    let publicURL = try supabase.storage

    2

      .from("public-bucket")

    3

      .getPublicURL(path: "folder/avatar1.png")

* * *

## Download a file

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Download fileDownload file with transformations


    1

    let data = try await supabase.storage

    2

      .from("avatars")

    3

      .download(path: "folder/avatar1.png")

* * *

## Delete files in a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `delete` and `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Delete file


    1

    try await supabase.storage

    2

      .from("avatars")

    3

      .remove(paths: ["folder/avatar1.png"])

* * *

## List all files in a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


List files in a bucketSearch files in a bucket


    1

    let files = try await supabase.storage

    2

      .from("avatars")

    3

      .list(

    4

        path: "folder",

    5

        options: SearchOptions(

    6

          limit: 100,

    7

          offset: 0,

    8

          sortBy: SortBy(column: "name", order: "asc")

    9

        )

    10

      )