---
title: "kotlinClient Library"
url: https://supabase.com/docs/reference/kotlin/introduction
---

# kotlinClient Library

Kotlin Reference v3.0

# kotlin Client Library

@supabase-community/supabase-kt[View on GitHub](https://github.com/supabase-community/supabase-kt)

This reference documents every object and method available in Supabase's Kotlin Multiplatform library, [supabase-kt](https://github.com/supabase-community/supabase-kt). You can use supabase-kt to interact with your Postgres database, listen to database changes, invoke Deno Edge Functions, build login and user management functionality, and manage large files.

To see supported Kotlin targets, check the corresponding module README on [GitHub](https://github.com/supabase-community/supabase-kt).

To migrate from version 2.X to 3.0.0, see the [migration guide](https://github.com/supabase-community/supabase-kt/blob/master/MIGRATION.md)

The Kotlin client library is created and maintained by the Supabase community, and is not an official library. Please be tolerant of areas where the library is still being developed, and — as with all the libraries — feel free to contribute wherever you find issues.

Huge thanks to official maintainer, [jan-tennert](https://github.com/jan-tennert).

* * *

## Installing

### Add one or more modules to your project#

[](https://github.com/supabase-community/supabase-kt/releases)[](https://github.com/supabase-community/supabase-kt/releases)

Add dependency to your build file using the BOM.

The available modules are:

  * [**auth-kt**](https://github.com/supabase-community/supabase-kt/tree/master/Auth)
  * [**realtime-kt**](https://github.com/supabase-community/supabase-kt/tree/master/Realtime)
  * [**storage-kt**](https://github.com/supabase-community/supabase-kt/tree/master/Storage)
  * [**functions-kt**](https://github.com/supabase-community/supabase-kt/tree/master/Functions)
  * [**postgrest-kt**](https://github.com/supabase-community/supabase-kt/tree/master/Postgrest)
  * Other plugins also available [here](https://github.com/supabase-community/supabase-kt/tree/master/plugins)


Check out the different READMEs for information about supported Kotlin targets.

_Note that the minimum Android SDK version is 26. For lower versions, you need to enable[core library desugaring](https://developer.android.com/studio/write/java8-support#library-desugaring)._

build.gradle.ktsbuild.gradlepom.xml


    1

    implementation(platform("io.github.jan-tennert.supabase:bom:VERSION"))

    2

    implementation("io.github.jan-tennert.supabase:postgrest-kt")

    3

    implementation("io.github.jan-tennert.supabase:auth-kt")

    4

    implementation("io.github.jan-tennert.supabase:realtime-kt")

### Add Ktor Client Engine to each of your Kotlin targets (required)#

You can find a list of engines [here](https://ktor.io/docs/http-client-engines.html)

  * Note that not all Ktor engines support WebSockets. So if you plan to use the Realtime module, make sure to use an engine that supports WebSockets. Check out the [engine limitations](https://ktor.io/docs/client-engines.html#limitations) for more information.
  * If using `supabase-kt` 3.0.0 and above, you need to use Ktor version 3.0.0-rc-1 or later.


build.gradle.ktsbuild.gradlepom.xml


    1

    implementation("io.ktor:ktor-client-[engine]:KTOR_VERSION")

Multiplatform example:

build.gradle.kts


    1

    commonMain {

    2

        dependencies {

    3

            //Supabase modules

    4

        }

    5

    }

    6

    jvmMain {

    7

        dependencies {

    8

            implementation("io.ktor:ktor-client-cio:KTOR_VERSION")

    9

        }

    10

    }

    11

    androidMain {

    12

        dependsOn(jvmMain.get())

    13

    }

    14

    jsMain {

    15

        dependencies {

    16

            implementation("io.ktor:ktor-client-js:KTOR_VERSION")

    17

        }

    18

    }

    19

    iosMain {

    20

        dependencies {

    21

            implementation("io.ktor:ktor-client-darwin:KTOR_VERSION")

    22

        }

    23

    }

### Serialization#

supabase-kt provides several different ways to encode and decode your custom objects. By default, [KotlinX Serialization](https://github.com/Kotlin/kotlinx.serialization) is used.

Use [KotlinX Serialization](https://github.com/Kotlin/kotlinx.serialization).

build.gradle.ktsbuild.gradlepom.xml


    1

    plugins {

    2

        kotlin("plugin.serialization") version "KOTLIN_VERSION"

    3

    }


    1

    val supabase = createSupabaseClient(supabaseUrl, supabaseKey) {

    2

        //Already the default serializer, but you can provide a custom Json instance (optional):

    3

        defaultSerializer = KotlinXSerializer(Json {

    4

            //apply your custom config

    5

        })

    6

    }

Use [Moshi](https://github.com/square/moshi).

build.gradle.ktsbuild.gradlepom.xml


    1

    implementation("io.github.jan-tennert.supabase:serializer-moshi:VERSION")


    1

    val supabase = createSupabaseClient(supabaseUrl, supabaseKey) {

    2

        defaultSerializer = MoshiSerializer()

    3

    }

Use [Jackson](https://github.com/FasterXML/jackson-module-kotlin).

build.gradle.ktsbuild.gradlepom.xml


    1

    implementation("io.github.jan-tennert.supabase:serializer-jackson:VERSION")


    1

    val supabase = createSupabaseClient(supabaseUrl, supabaseKey) {

    2

        defaultSerializer = JacksonSerializer()

    3

    }

Use custom serializer.


    1

    class CustomSerializer: SupabaseSerializer {

    2

    3

        override fun <T : Any> encode(type: KType, value: T): String {

    4

            //encode value to string

    5

        }

    6

    7

        override fun <T : Any> decode(type: KType, value: String): T {

    8

            //decode value

    9

        }

    10

    11

    }


    1

    val supabase = createSupabaseClient(supabaseUrl, supabaseKey) {

    2

        defaultSerializer = CustomSerializer()

    3

    }

### Enable Data API access#

supabase-kt uses the Data API to query and mutate your Postgres data. You first need to grant Data API roles permissions to access your tables and functions.

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

### Create Supabase Client#

Independently of which Supabase module you are using, you will need to initialize the main client first and install the module.

To create a new client, you can use the `createSupabaseClient` function.

When installing a module, you can pass a block to configure it.

### OAuth and OTP link verification#

[supabase-kt](https://github.com/supabase-community/supabase-kt) provides several platform implementations for OAuth and OTP link verification.

OAuth sign-ins can be triggered via `supabase.auth.signInWith(Provider)` and the callback has to be handled differently on each Kotlin target. OTP link verification stands for opening your app, and directly signing-in via the clicked link. This is only supported on Mobile devices & Web targets.

The following will explain how each target handles OAuth and OTP link verification, if supported.

**On Desktop platforms (JVM, MacOS*, Linux, Windows)** , it uses a HTTP Callback Server to receive the session data from a successful OAuth login. The success page can be customized via `AuthConfig#httpCallbackConfig`
* If no deeplinks are being used. This works as-is, by default.

_Note: OTP link verification such as sign ups are not supported on JVM. You may have to send a verification token rather than a url in your email. To send the token, rather than a redirect url, change`{{ .ConfirmationURL }}` in your sign up email to `{{ .Token }}`_

**On Android, iOS & MacOS**, OAuth and OTP verification use deeplinks. Refer to the guide below on how to setup deeplinks. Alternatively you can use [Native Google Auth](/docs/guides/auth/social-login/auth-google?platform=android).
**On JS** , it uses the website origin as the callback url. Session importing gets handled automatically.
**tvOS & watchOS** currently have no default implementation. Feel free to create a PR.

You always make your own implementation and use `auth.parseSessionFromFragment(fragment)` or `auth.parseSessionFromUrl(url)` to let [supabase-kt](https://github.com/supabase-community/supabase-kt) handle the parsing after receiving a callback. Then you can simply use `auth.importSession(session)`.

### Configure deeplink callbacks for Authentication#

Deeplinks are supported on Android, iOS and MacOS.

  1. **Set up a deeplink**
On Android, set up a [deeplink](https://developer.android.com/training/app-links/deep-linking) in your Android manifest.
On iOS and MacOS, set up a [url scheme](https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app).
  2. **Add your deeplink to the[redirect URLs](https://supabase.com/dashboard/project/_/auth/url-configuration)**
**Pattern** : scheme://host
  3. **Configure the Auth plugin** Set the `host` and `scheme` in the Auth config:

         1

         install(Auth) {

         2

            host = "deeplink host" // this can be anything, eg. your package name or app/company url (not your Supabase url)

         3

            scheme = "deeplink scheme"

         4

         5

            // On Android only, you can set OAuth and SSO logins to open in a custom tab, rather than an external browser:

         6

            defaultExternalAuthAction = ExternalAuthAction.CustomTabs() //defaults to ExternalAuthAction.ExternalBrowser

         7

         }

  4. **Call platform specific function on startup**
On Android: `supabase.handleDeeplinks(intent)`
If you don't want a separate activity, just call this function at the top of your `onCreate` function in your MainActivity.
On iOS/MacOS: `supabase.handleDeeplinks(url)`


Then you can log in using OAuth:


    1

    supabase.auth.signInWith(Google)

Or open OTP links directly in your app.

### PKCE Authentication flow#

supabase-kt supports the [PKCE authentication flow](https://supabase.com/blog/supabase-auth-sso-pkce). To use it, change the `flowType` in the Auth configuration:


    1

    install(Auth) {

    2

      flowType = FlowType.PKCE

    3

    }

That's it! If you already implemented deeplinks to handle OTPs and OAuth you don't have to change anything!

### Parameters

  * supabaseUrl

Required

String

The unique Supabase URL which is supplied when you create a new project in your project dashboard.

  * supabaseKey

Required

String

The unique Supabase Key which is supplied when you create a new project in your project dashboard.

  * builder

Optional

SupabaseClientBuilder.() -> Unit

Apply additional configuration and install plugins.

Details


Initialize ClientConfigure Auth moduleConfigure PostgREST moduleConfigure Storage moduleConfigure Realtime moduleConfigure Functions pluginConfigure GraphQL plugin


    1

    val supabase = createSupabaseClient(

    2

        supabaseUrl = "https://xyzcompany.supabase.co",

    3

        supabaseKey = "your-publishable-key"

    4

    ) {

    5

        install(Auth)

    6

        install(Postgrest)

    7

        //install other modules

    8

    }

* * *

## Fetch data

Perform a SELECT query on the table or view.

  * When calling a `decode` method, you have to provide a [serializable class](/docs/reference/kotlin/installing#serialization) as the type parameter.
  * You can provide a `Columns` object to select specific columns.
  * You can provide a [filter](/docs/reference/kotlin/using-filters) block to filter the results


### Parameters

  * columns

Optional

Columns

The columns to retrieve, defaults to `Columns.ALL`. You can also use `Columns.list`, `Columns.type` or `Columns.raw` to specify the columns.

  * head

Optional

Boolean

If true, select will delete the selected data.

  * request

Optional

PostgrestRequestBuilder.() -> Unit

Additional configuration & filtering for the request.


Getting your dataSelecting specific columnsQuery foreign tablesQuery the same foreign table multiple timesQuerying with count optionQuerying JSON data


    1

    val city = supabase.from("cities").select().decodeSingle<City>()

* * *

## Insert data

Perform an INSERT into the table or view.

  * When calling an `insert` method, you have to provide a [serializable value](/docs/reference/kotlin/installing#serialization).
  * By default, `insert` will not return the inserted data. If you want to return the inserted data, you can use the `select()` method inside the request.


### Parameters

  * value

Required

T or List<T>

The value(s) you want to insert. `T` can be any serializable type.

  * request

Optional

PostgrestRequestBuilder.() -> Unit

Additional configuration & filtering for the request.


Create a recordCreate a record and return itBulk create


    1

    val city = City(name = "The Shire", countryId = 554)

    2

    supabase.from("cities").insert(city)

* * *

## Update data

Perform an UPDATE on the table or view.

  * `update()` should always be combined with a [filter](/docs/reference/kotlin/using-filters) block to avoid updating all records.
  * When calling `insert` or `update`, you have to provide a [serializable value](/docs/reference/kotlin/installing#serialization) in the function parameter.
  * By default, `update` will not return the inserted data. If you want to return the inserted data, you can use the `select()` method inside the request.


### Parameters

  * value

Required

T or PostgrestUpdate.() -> Unit = {}

The new value, can be either a serializable value or PostgrestUpdate DSL where you can set new values per column.

  * request

Optional

PostgrestRequestBuilder.() -> Unit

Additional configuration & filtering for the request.


Updating your dataUpdate a record and return itUpdating JSON data


    1

    supabase.from("characters").update(

    2

        {

    3

           Country::name setTo "Han Solo"

    4

           //or

    5

           set("name", "Han Solo")

    6

        }

    7

    ) {

    8

        filter {

    9

            Character::id eq 1

    10

            //or

    11

            eq("id", 1)

    12

        }

    13

    }

* * *

## Upsert data

Perform an UPSERT on the table or view. Depending on the column(s) passed to `onConflict`, `.upsert()` allows you to perform the equivalent of `.insert()` if a row with the corresponding `onConflict` columns doesn't exist, or if it does exist, perform an alternative action depending on `ignoreDuplicates`.

  * Primary keys should be included in the data payload in order for an update to work correctly.
  * Primary keys must be natural, not surrogate. There are however, [workarounds](https://github.com/PostgREST/postgrest/issues/1118) for surrogate primary keys.
  * If you need to insert new data and update existing data at the same time, use [Postgres triggers](https://github.com/supabase/postgrest-js/issues/173#issuecomment-825124550).
  * When calling `insert` or `update`, you have to provide a [serializable value](/docs/reference/kotlin/installing#serialization) in the function parameter.
  * By default, `upsert` will not return the inserted data. If you want to return the inserted data, you can use the `select()` method inside the request.


### Parameters

  * value

Required

T or List<T>

The value(s) you want to insert. `T` can be any serializable type.

  * request

Optional

PostgrestRequestBuilder.() -> Unit

Additional configuration & filtering for the request.


Upsert your dataUpsert your data and return itUpserting into tables with constraintsReturn the exact number of rows


    1

    val toUpsert = Message(id = 3, message = "foo", username = "supabot")

    2

    supabase.from("messages").upsert(toUpsert)

* * *

## Delete data

Perform a DELETE on the table or view.

  * `delete()` should always be combined with a [filter](/docs/reference/kotlin/using-filters) block to target the item(s) you wish to delete.
  * If you use `delete()` with filters and you have [RLS](/docs/learn/auth-deep-dive/auth-row-level-security) enabled, only rows visible through `SELECT` policies are deleted. Note that by default no rows are visible, so you need at least one `SELECT`/`ALL` policy that makes the rows visible.
  * By default, `delete` will not return the deleted data. If you want to return the deleted data, you can use the `select()` method inside the request.


### Parameters

  * request

Optional

PostgrestRequestBuilder.() -> Unit

Additional configuration & filtering for the request.


Delete recordsFetch deleted records


    1

    supabase.from("cities").delete {

    2

        filter {

    3

            City::id eq 666

    4

            //or

    5

            eq("id", 666)

    6

        }

    7

    }

* * *

## Call a Postgres function

You can call functions as a "Remote Procedure Call".

That's a fancy way of saying that you can put some logic into your database then call it from anywhere. It's especially useful when the logic rarely changes - like password resets and updates.

  * When calling `rpc` with parameters, you have to provide a [serializable value](/docs/reference/kotlin/installing#serialization) in the function parameter.


### Parameters

  * function

Required

String

The name of the function

  * parameters

Optional

T

Parameters to pass to the function. T can be any serializable type.

  * request

Optional

PostgrestRequestBuilder.() -> Unit

Additional configuration & filtering for the request.


Call a database functionWith Parameters


    1

    supabase.postgrest.rpc("hello_world")

Notes

* * *

## Using filters

Filters allow you to only return rows that match certain conditions.

Filters can be used on `select()`, `update()`, and `delete()` queries.

You can use two different types for applying filters:


    1

    eq("country_id", 1)

And using a class property:


    1

    City::countryId eq 1

As you can see on the property syntax: the name of the `countryId` gets converted to `country_id`.

By default, this is done by converting camel case to snake case, but you can customize this by changing the `propertyConversionMethod` in the Postgrest Config

If a database function returns a table response, you can also apply filters.

Applying a filter blockMultiple filters on one columnFilter by values within a JSON columnFilter Foreign Tables


    1

    supabase.from("cities").select(columns = Columns.list("name", "country_id")) {

    2

        filter {

    3

            City::name eq "The Shire"

    4

            //or

    5

            eq("name", "The Shire")

    6

        }

    7

    }

Notes

* * *

## Column is equal to a value

Finds all rows whose value on the stated `column` exactly matches the specified `value`.

### Parameters

  * column

Required

String

The column to filter on.

  * value

Required

Any

The value to filter with.


With `select()`With `update()`With `delete()`With `rpc()`


    1

    supabase.from("cities").select(columns = Columns.list("name", "country_id")) {

    2

        filter {

    3

            City::name eq "The Shire"

    4

            //or

    5

            eq("name", "The Shire")

    6

        }

    7

    }

* * *

## Column is not equal to a value

Finds all rows whose value on the stated `column` doesn't match the specified `value`.

### Parameters

  * column

Required

String

The column to filter on.

  * value

Required

Any

The value to filter with.


With `select()`With `update()`With `delete()`With `rpc()`


    1

    supabase.from("cities").select(columns = Columns.list("name", "country_id")) {

    2

        filter {

    3

            City::name neq "The Shire"

    4

            //or

    5

            neq("name", "The Shire")

    6

        }

    7

    }

* * *

## Column is greater than a value

Finds all rows whose value on the stated `column` is greater than the specified `value`.

### Parameters

  * column

Required

String

The column to filter on.

  * value

Required

Any

The value to filter with.


With `select()`With `update()`With `delete()`With `rpc()`


    1

    supabase.from("cities").select(columns = Columns.list("name")) {

    2

        filter {

    3

           City::countryId gt 300

    4

           //or

    5

           gt("country_id", 300)

    6

        }

    7

    }

* * *

## Column is greater than or equal to a value

Finds all rows whose value on the stated `column` is greater than or equal to the specified `value`.

### Parameters

  * column

Required

String

The column to filter on.

  * value

Required

Any

The value to filter with.


With `select()`With `update()`With `delete()`With `rpc()`


    1

    supabase.from("cities").select(columns = Columns.list("name")) {

    2

        filter {

    3

           City::countryId gte 300

    4

           //or

    5

           gte("country_id", 300)

    6

        }

    7

    }

* * *

## Column is less than a value

Finds all rows whose value on the stated `column` is less than the specified `value`.

### Parameters

  * column

Required

String

The column to filter on.

  * value

Required

Any

The value to filter with.


With `select()`With `update()`With `delete()`With `rpc()`


    1

    supabase.from("cities").select(columns = Columns.list("name")) {

    2

        filter {

    3

           City::countryId lt 300

    4

           //or

    5

           lt("country_id", 300)

    6

        }

    7

    }

* * *

## Column is less than or equal to a value

Finds all rows whose value on the stated `column` is less than or equal to the specified `value`.

### Parameters

  * column

Required

String

The column to filter on.

  * value

Required

Any

The value to filter with.


With `select()`With `update()`With `delete()`With `rpc()`


    1

    supabase.from("cities").select(columns = Columns.list("name")) {

    2

        filter {

    3

           City::countryId lte 300

    4

           //or

    5

           lte("country_id", 300)

    6

        }

    7

    }

* * *

## Column matches a pattern

Finds all rows whose value in the stated `column` matches the supplied `pattern` (case sensitive).

### Parameters

  * column

Required

String

The column to filter on.

  * pattern

Required

String

The pattern to match with.


With `select()`With `update()`With `delete()`With `rpc()`


    1

    supabase.from("cities").select(columns = Columns.list("name")) {

    2

        filter {

    3

           City::name like "%la%"

    4

           //or

    5

           like("name", "%la%")

    6

        }

    7

    }

* * *

## Column matches a case-insensitive pattern

Finds all rows whose value in the stated `column` matches the supplied `pattern` (case insensitive).

### Parameters

  * column

Required

String

The column to filter on.

  * pattern

Required

String

The pattern to match with.


With `select()`With `update()`With `delete()`With `rpc()`


    1

    supabase.from("cities").select(columns = Columns.list("name")) {

    2

        filter {

    3

           City::name ilike "%la%"

    4

           //or

    5

           ilike("name", "%la%")

    6

        }

    7

    }

* * *

## Column is a value

A check for exact equality (null, true, false), finds all rows whose value on the stated `column` exactly match the specified `value`.

`is_` and `in_` filter methods are suffixed with `_` to avoid collisions with reserved keywords.

### Parameters

  * column

Required

String

The column to filter on.

  * value

Required

Boolean?

The value to filter with.


With `select()`With `update()`With `delete()`With `rpc()`


    1

    supabase.from("cities").select(columns = Columns.list("name")) {

    2

        filter {

    3

           City::name isExact null

    4

           //or

    5

           exact("name", null)

    6

        }

    7

    }

* * *

## Column is in an array

Finds all rows whose value on the stated `column` is found on the specified `values`.

### Parameters

  * column

Required

String

The column to filter on.

  * values

Required

List<Any>

The values to filter with.


With `select()`With `update()`With `delete()`With `rpc()`


    1

    supabase.from("cities").select(columns = Columns.list("name")) {

    2

        filter {

    3

           City::name isIn listOf("Hobbiton", "Edoras")

    4

           //or

    5

           isIn("name", listOf("Hobbiton", "Edoras"))

    6

        }

    7

    }

* * *

## Column contains every element in a value

Only relevant for jsonb, array, and range columns. Match only rows where `column` contains every element appearing in `value`.

### Parameters

  * column

Required

String

The jsonb, array, or range column to filter on

  * value

Required

List<Any>

The jsonb, array, or range value to filter with


With `select()`With `update()`With `delete()`With `rpc()`


    1

    supabase.from("cities").select(columns = Columns.list("name")) {

    2

        filter {

    3

           City::mainExports contains listOf("oil")

    4

           //or

    5

           contains("main_exports", listOf("oil"))

    6

        }

    7

    }

* * *

## Greater than a range

Only relevant for range columns. Match only rows where every element in column is greater than any element in range.

### Parameters

  * column

Required

String

The column to filter on.

  * values

Required

Pair<Any, Any>

The values to filter with.


With `select()`


    1

    supabase.from("reservations").select {

    2

          filter {

    3

             Reservation::during rangeGt ("2000-01-02 08:30" to "2000-01-02 09:30")

    4

             //or

    5

             rangeGt("during", "2000-01-02 08:30" to "2000-01-02 09:30")

    6

          }

    7

      }

Data source

Response

Notes

* * *

## Greater than or equal to a range

Only relevant for range columns. Match only rows where every element in column is either contained in range or greater than any element in range.

### Parameters

  * column

Required

String

The column to filter on.

  * values

Required

Pair<Any, Any>

The values to filter with.


With `select()`


    1

    supabase.from("reservations").select {

    2

          filter {

    3

             Reservation::during rangeGte ("2000-01-02 08:30" to "2000-01-02 09:30")

    4

             //or

    5

             rangeGte("during", "2000-01-02 08:30" to "2000-01-02 09:30")

    6

          }

    7

      }

Data source

Response

Notes

* * *

## Less than a range

Only relevant for range columns. Match only rows where every element in column is less than any element in range.

### Parameters

  * column

Required

String

The column to filter on.

  * values

Required

Pair<Any, Any>

The values to filter with.


With `select()`


    1

    supabase.from("reservations").select {

    2

          filter {

    3

             Reservation::during rangeLt ("2000-01-02 08:30" to "2000-01-02 09:30")

    4

             //or

    5

             rangeLt("during", "2000-01-02 08:30" to "2000-01-02 09:30")

    6

          }

    7

      }

Data source

Response

Notes

* * *

## Less than or equal to a range

Only relevant for range columns. Match only rows where every element in column is either contained in range or less than any element in range.

### Parameters

  * column

Required

String

The column to filter on.

  * values

Required

Pair<Any, Any>

The values to filter with.


With `select()`


    1

    supabase.from("reservations").select {

    2

          filter {

    3

             Reservation::during rangeLte ("2000-01-02 08:30" to "2000-01-02 09:30")

    4

             //or

    5

             rangeLte("during", "2000-01-02 08:30" to "2000-01-02 09:30")

    6

          }

    7

      }

Data source

Response

Notes

* * *

## Mutually exclusive to a range

Only relevant for range columns. Match only rows where column is mutually exclusive to range and there can be no element between the two ranges.

### Parameters

  * column

Required

String

The column to filter on.

  * values

Required

Pair<Any, Any>

The values to filter with.


With `select()`


    1

    supabase.from("reservations").select {

    2

          filter {

    3

             Reservation::during adjacent ("2000-01-02 08:30" to "2000-01-02 09:30")

    4

             //or

    5

             adjacent("during", "2000-01-02 08:30" to "2000-01-02 09:30")

    6

          }

    7

      }

Data source

Response

* * *

## With a common element

Only relevant for array and range columns. Match only rows where column and value have an element in common.

### Parameters

  * column

Required

String

The column to filter on.

  * values

Required

List<Any>

The values to filter with.


On array columnsOn range columns


    1

    supabase.from("issues").select(columns = Columns.list("title")) {

    2

        filter {

    3

           Issue::tags overlaps listOf("is:closed", "severity:high")

    4

           //or

    5

           overlaps("tags", listOf("is:closed", "severity:high"))

    6

        }

    7

    }

Data source

Response

* * *

## Match a string

Only relevant for text and tsvector columns. Match only rows where `column` matches the query string in `query`.

For more information, see [Postgres full text search](https://supabase.com/docs/guides/database/full-text-search).

### Parameters

  * column

Required

String

The text or tsvector column to filter on

  * query

Required

String

The query text to match with

  * textSearchType

Optional

TextSearchType

The type of text search to use. Defaults to `TextSearchType.NONE`.

  * config

Optional

String

The text search configuration to use.


Text searchSearch multiple columnsBasic normalizationFull normalizationWebsearch


    1

    supabase.from("quotes").select(columns = Columns.list("catchphrase")) {

    2

        filter {

    3

           textSearch(column = "catchphrase", query = "'fat' & 'cat'", config = "english", type = TextSearchType.YOUR_TYPE)

    4

        }

    5

    }

* * *

## Don't match the filter

Finds all rows that don't satisfy the filter.

  * `.filterNot()` expects you to use the raw [PostgREST syntax](https://postgrest.org/en/stable/api.html#horizontal-filtering-rows) for the filter names and values.


### Parameters

  * column

Required

String

The column to filter on.

  * operator

Required

FilterOperator

The operator to use for the filter.

  * value

Required

Any

The value to filter with.


With `select()`


    1

    supabase.from("countries").select {

    2

        filter {

    3

            filterNot("name", FilterOperator.IS, "")

    4

        }

    5

    }

* * *

## Match at least one filter

Finds all rows satisfying at least one of the filters.

### Parameters

  * negate

Optional

Boolean

If true, negate the entire block.

  * block

Required

PostgrestFilterBuilder.() -> Unit

The block to apply the `or` filter to.


With `select()`Use `or` with `and`


    1

    supabase.from("countries").select(columns = Columns.list("name")) {

    2

        filter {

    3

            or {

    4

                Country::id eq 2

    5

                Country::name eq "Mordor"

    6

                //or

    7

                eq("id", 2)

    8

                eq("name", "Mordor")

    9

            }

    10

        }

    11

    }

* * *

## Match the filter

filter() expects you to use the raw PostgREST syntax for the filter values.

### Parameters

  * column

Required

String

The column to filter on.

  * operator

Required

FilterOperator

The operator to use for the filter.

  * value

Required

Any

The value to filter with.


With `select()`On a foreign table


    1

    supabase.from("characters").select {

    2

        filter {

    3

           filter(column = "name", operator = FilterOperator.IN, value = "('Han', 'Katniss')")

    4

        }

    5

    }

Data source

Response

* * *

## Using modifiers

Filters work on the row level—they allow you to return rows that only match certain conditions without changing the shape of the rows. Modifiers are everything that don't fit that definition—allowing you to change the format of the response (e.g., returning a CSV string).

Modifiers are be specified next to the filter block. Some modifiers only apply for queries that return rows (e.g., `select()` or `rpc()` on a function that returns a table response).

* * *

## Return data after inserting

### Parameters

  * columns

Optional

Columns

The columns to select.


With `upsert()`


    1

    val toUpsert = Country(id = 2, name = "Mordor")

    2

    val count = supabase.from("countries").upsert(toUpsert) {

    3

        select()

    4

    }.decodeSingle<Country>()

Data source

Response

* * *

## Order the results

Order the query result by column.

### Parameters

  * column

Required

String

The column to order by.

  * order

Required

Order

The order to use.

  * nullsFirst

Optional

Boolean

Whether to order nulls first.

  * referencedTable

Optional

String

The foreign table to order by.


With `select()`On a foreign tableOrder parent table by a referenced table


    1

    supabase.from("characters").select(columns = Columns.list("id", "name")) {

    2

       order(column = "id", order = Order.DESCENDING)

    3

    }

Data source

Response

* * *

## Limit the number of rows returned

Limit the query result by count.

### Parameters

  * count

Required

Long

The number of rows to limit the result to.

  * referencedTable

Optional

String

The foreign table to limit by.


With `select()`On a foreign table


    1

    supabase.from("characters").select {

    2

        limit(count = 1)

    3

    }

Data source

Response

* * *

## Limit the query to a range

Limit the query result by from and to inclusively.

### Parameters

  * from

Required

Long

The start of the range.

  * to

Required

Long

The end of the range.

  * referencedTable

Optional

String

The foreign table to limit by.


With `select()`


    1

    supabase.from("characters").select {

    2

        range(1L..5L)

    3

    }

Data source

Response

* * *

## Retrieve one row of data

With `select()`


    1

    val result = supabase.from("characters").select(Columns.list("name")) {

    2

        limit(1)

    3

        single()

    4

    }

Data source

Response

* * *

## Retrieve as a CSV

Return data as CSV


    1

    val (csvData, _) = supabase.from("characters").select {

    2

        csv()

    3

    }

Data source

Response

Notes

* * *

## Strip null values

Strip null values from the response.

  * Requires PostgREST 11.2.0 or later.


Strip null values


    1

    val data = supabase

    2

      .from("characters")

    3

      .select {

    4

        stripNulls()

    5

      }

* * *

## Using explain

For debugging slow queries, you can get the [Postgres `EXPLAIN` execution plan](https://www.postgresql.org/docs/current/sql-explain.html) of a query using the `explain()` method. This works on any query, even for `rpc()` or writes.

Explain is not enabled by default as it can reveal sensitive information about your database. It's best to only enable this for testing environments but if you wish to enable it for production you can provide additional protection by using a `pre-request` function.

Follow the [Performance Debugging Guide](/docs/guides/api/rest/debugging-performance) to enable the functionality on your project.

### Parameters

  * analyze

Optional

Boolean

If `true`, the query will be executed and the actual run time will be returned

  * verbose

Optional

Boolean

If `true`, the query identifier will be returned and `data` will include the output columns of the query

  * settings

Optional

Boolean

If `true`, include information on configuration parameters that affect query planning

  * buffers

Optional

Boolean

If `true`, include information on buffer usage

  * wal

Optional

Boolean

If `true`, include information on WAL record generation

  * format

Optional

String

The format of the output, can be `"text"` (default) or `"json"`


Get the execution plan


    1

    val result = supabase.from("characters").select {

    2

        explain()

    3

    }

Data source

Response

Notes

* * *

## Overview

  * The auth methods can be accessed via the Supabase Auth client.


Create auth client


    1

    val supabase = createSupabaseClient(supabaseUrl = "https://xyzcompany.supabase.co", supabaseKey = "your-publishable-key") { ... }

    2

    val auth = supabase.auth

* * *

## Create a new user

Creates a new user.

  * By default, the user needs to verify their email address before logging in. To turn this off, disable **Confirm email** in [your project](https://supabase.com/dashboard/project/_/auth/providers).
  * **Confirm email** determines if users need to confirm their email address after signing up.
    * If **Confirm email** is enabled, the return value is the user and you won't be logged in automatically.
    * If **Confirm email** is disabled, the return value is null and you will be logged in instead.
  * When the user confirms their email address, they are redirected to the [`SITE_URL`](https://supabase.com/docs/guides/auth/redirect-urls) by default. You can modify your `SITE_URL` or add additional redirect URLs in [your project](https://supabase.com/dashboard/project/_/auth/url-configuration).
  * To learn how to handle OTP links & OAuth refer to [initializing](/docs/reference/kotlin/initializing)
  * If signUpWith() is called for an existing confirmed user:
    * When both **Confirm email** and **Confirm phone** (even when phone provider is disabled) are enabled in [your project](/dashboard/project/_/auth/providers), an obfuscated/fake user object is returned.
    * When either **Confirm email** or **Confirm phone** (even when phone provider is disabled) is disabled, the error message, `User already registered` is returned.


### Parameters

  * provider

Required

Email or Phone

The provider to use for the user's authentication. In this case `Email` or `Phone`.

  * redirectUrl

Optional

String?

The redirect url to use. If you don't specify this, the platform specific will be used, like deeplinks on android.

  * config

Optional

Email.Config.() -> Unit or Phone.Config.() -> Unit

The configuration for signing in with `Email` or `Phone`.

Details


Sign up with emailSign up with a phone number and password (whatsapp)Sign up with a phone number and password (sms)Sign up with additional user metadataSign up with a redirect URL


    1

    val user = supabase.auth.signUpWith(Email) {

    2

        email = "example@email.com"

    3

        password = "example-password"

    4

    }

* * *

## Listen to auth events

Listen to session changes.

  * `sessionStatus` is a `Flow<SessionStatus>` that emits the current session status. Everything related to the session is handled by this flow.
  * `events` is a `Flow<AuthEvent>` that emits auth events. This flow is used to listen to auth events that are independent of the current session, like OTP errors, refresh failures, etc. Experimental.


Listen to session status changesListen to auth events


    1

    supabase.auth.sessionStatus.collect {

    2

        when(it) {

    3

            is SessionStatus.Authenticated -> {

    4

                println("Received new authenticated session.")

    5

                when(it.source) { //Check the source of the session

    6

                    SessionSource.External -> TODO()

    7

                    is SessionSource.Refresh -> TODO()

    8

                    is SessionSource.SignIn -> TODO()

    9

                    is SessionSource.SignUp -> TODO()

    10

                    SessionSource.Storage -> TODO()

    11

                    SessionSource.Unknown -> TODO()

    12

                    is SessionSource.UserChanged -> TODO()

    13

                    is SessionSource.UserIdentitiesChanged -> TODO()

    14

                }

    15

            }

    16

            SessionStatus.Initializing -> println("Initializing")

    17

            is SessionStatus.RefreshFailure -> {

    18

                println("Session expired and could not be refreshed")

    19

            }

    20

            is SessionStatus.NotAuthenticated -> {

    21

                if(it.isSignOut) {

    22

                    println("User signed out")

    23

                } else {

    24

                    println("User not signed in")

    25

                }

    26

            }

    27

        }

    28

    }

Notes

* * *

## Create an anonymous user

  * Creates an anonymous user.
  * The user can be retrieved by calling `supabase.auth.currentUserOrNull()`.
  * It is recommended to set up captcha for anonymous sign-ins to prevent abuse. You can pass in the captcha token in the `options` param.


### Parameters

  * captchaToken

Optional

String?

The captcha token when having captcha enabled.

  * data

Optional

JsonObject? or T

Extra user data to pass in.


Create an anonymous userCreate an anonymous user with custom user metadata


    1

    supabase.auth.signInAnonymously(captchaToken = "token")

* * *

## Sign in a user

Logs in an existing user.

  * Requires either an email and password or a phone number and password.


### Parameters

  * provider

Required

Email or Phone

The provider to use for the user's authentication, in this case `Email` or `Phone`.

  * redirectUrl

Optional

String?

The redirect url to use. If you don't specify this, the platform specific will be used, like deeplinks on android.

  * config

Optional

Email.Config.() -> Unit or Phone.Config.() -> Unit

The configuration for signing in with `Email` or `Phone`.

Details


Sign in with email and passwordSign in with phone and password


    1

    supabase.auth.signInWith(Email) {

    2

        email = "example@email.com"

    3

        password = "example-password"

    4

    }

* * *

## Sign in with ID token (native sign-in)

### Parameters

  * provider

Required

IDToken

The provider to use for the user's authentication. For this method it will be `IDToken`.

  * redirectUrl

Optional

String?

The redirect url to use. If you don't specify this, the platform specific will be used, like deeplinks on android.

  * config

Optional

IDToken.Config.() -> Unit

The configuration for signing in with an id token.

Details


Sign In using ID Token


    1

    supabase.auth.signInWith(IDToken) {

    2

        idToken = "token"

    3

        provider = Google //Also supported: Apple, Azure and Facebook

    4

        //optional:

    5

        nonce = "nonce"

    6

    }

* * *

## Sign in a user through OTP

Sends a OTP to the user's email or phone number.

  * Requires either an email or phone number.
  * This method is used for passwordless sign-ins where a OTP is sent to the user's email or phone number.
  * If the user doesn't exist, `signInWith(OTP)` will signup the user instead. To restrict this behavior, you can set `createUser` to `false`.
  * The method `signUpWith(OTP)` does the exact same thing as `signInWith(OTP)`, so it doesn't matter which one you use.
  * If you're using an email, you can configure whether you want the user to receive a magiclink or a OTP.
  * If you're using phone, you can configure whether you want the user to receive a OTP.
  * The magic link's destination URL is determined by the [`SITE_URL`](/docs/guides/auth/redirect-urls).
  * See [redirect URLs and wildcards](/docs/guides/auth/overview#redirect-urls-and-wildcards) to add additional redirect URLs to your project.
  * To learn how to handle OTP links & OAuth refer to [initializing](/docs/reference/kotlin/initializing)
  * Magic links and OTPs share the same implementation. To send users a one-time code instead of a magic link, [modify the magic link email template](https://supabase.com/dashboard/project/_/auth/templates) to include `{{ .Token }}` instead of `{{ .ConfirmationURL }}`.


### Parameters

  * provider

Required

OTP

The provider to use for the user's authentication, in this case `OTP`.

  * redirectUrl

Optional

String?

The redirect url to use. If you don't specify this, the platform specific will be used, like deeplinks on android.

  * config

Optional

OTP.Config.() -> Unit

The configuration for signing in with `OTP`.

Details


Sign in with emailSign in with SMS OTPSign in with WhatsApp OTP


    1

    supabase.auth.signInWith(OTP) {

    2

        email = "example@email.com"

    3

    }

Notes

* * *

## Sign in a user through OAuth

  * This method is used for signing in using a third-party provider.
  * Supabase supports many different [third-party providers](https://supabase.com/docs/guides/auth#providers).
  * To learn how to handle OTP links & OAuth refer to [initializing](/docs/reference/kotlin/initializing)
  * The `signInWith(Provider)` method can be called anywhere, you can treat it like any other suspending function.
  * This method will not wait for the OAuth response and will return after the corresponding platform implementation was called, which is by default opening the system browser.
  * The callback will happen differently on the targets. On Mobile devices it will be handled via `handleDeeplinks()`, if set up. On Desktop and JS devices it will be automatically handled.


### Parameters

  * provider

Required

OAuthProvider

The OAuth provider to use for the user's authentication, for example `Google` or `GitHub`.

  * redirectUrl

Optional

String?

The redirect url to use. If you don't specify this, the platform specific will be used, like deeplinks on android.

  * config

Optional

ExternalAuthConfig.() -> Unit

The configuration for signing in with an OAuth provider.

Details


Sign in using a third-party providerSign in using a third-party provider with scopesCreate a custom urlCreate a custom url with scopes


    1

    supabase.auth.signInWith(Github)

* * *

## Sign in a user through SSO

  * Before you can call this method you need to [establish a connection](/docs/guides/auth/sso/auth-sso-saml#managing-saml-20-connections) to an identity provider. Use the [CLI commands](/docs/reference/cli/supabase-sso) to do this.
  * If you've associated an email domain to the identity provider, you can change the `domain` property in the `signInWith(SSO)` method to start a sign-in flow.
  * In case you need to use a different way to start the authentication flow with an identity provider, you can change the `providerId` property. For example:
    * Mapping specific user email addresses with an identity provider.
    * Using different hints to identity the identity provider to be used by the user, like a company-specific page, IP address or other tracking information.
  * To learn how to handle OTP links & OAuth refer to [initializing](/docs/reference/kotlin/initializing)


### Parameters

  * provider

Required

SSO

The OAuth provider to use for the user's authentication, in this case `SSO`.

  * redirectUrl

Optional

String?

The redirect url to use. If you don't specify this, the platform specific will be used, like deeplinks on android.

  * config

Optional

ExternalAuthConfig.() -> Unit

The configuration for signing in with an OAuth provider.

Details


Sign in with email domainSign in with provider UUID


    1

    // You can extract the user's email domain and use it to trigger the

    2

      // authentication flow with the correct identity provider.

    3

    4

      supabase.auth.signInWith(SSO) {

    5

          domain = "company.com"

    6

      }

    7

    8

      //the url was opened automatically, if you don't want that, provide a custom redirect url

* * *

## Get user claims from verified JWT

Extracts the JWT claims from the access token by first verifying the JWT against the server's JSON Web Key Set (JWKS) endpoint.

  * Prefer this method over `retrieveUser()` as JWKS responses are cached, resulting in significantly faster responses.
  * If the project is not using an asymmetric JWT signing key (like ECC or RSA), it sends a request to the Auth server (similar to `retrieveUser()`) to verify the JWT.
  * Returns a `ClaimsResponse` containing `claims` (a `JwtPayload` with typed accessors for standard JWT fields), `header`, and `signature`.
  * Standard claims available on `JwtPayload`: `iss`, `sub`, `aud`, `exp`, `iat`, `role`, `aal`, `sessionId`, `email`, `phone`, `isAnonymous`, `amr`, `appMetadata`, `userMetadata`.
  * Use `claims.getClaim<T>(key)` or `claims.getClaimOrNull<T>(key)` for custom claims.


### Parameters

  * jwt

Optional

String?

An optional specific JWT to verify. If not provided, uses the current session's access token.

  * options

Optional

ClaimsRequestBuilder.() -> Unit

Options to customize the behavior, such as allowing expired tokens.

Details


Get claims from current sessionGet a custom claimVerify a specific JWT


    1

    val response = supabase.auth.getClaims()

    2

    val email = response.claims.email

    3

    val role = response.claims.role

    4

    val aal = response.claims.aal

* * *

## Sign out a user

Logs out the current user.

  * In order to use the `signOut()` method, the user needs to be signed in first.


### Parameters

  * scope

Optional

SignOutScope

The scope of the sign-out.


Sign outSign out all sessionsSign out all sessions except the current


    1

    supabase.auth.signOut()

* * *

## Send a password reset request

Sends a password reset request to the given email address.

  * The password reset flow consist of 2 broad steps: (i) Allow the user to login via the password reset link; (ii) Update the user's password.
  * The `resetPasswordForEmail()` only sends a password reset link to the user's email. To update the user's password, see [`updateUser()`](/docs/reference/kotlin/auth-updateuser).
  * The user gets redirected back to your app, assuming you setup [OTP handling](/docs/reference/kotlin/initializing)
  * After the user has been redirected successfully, prompt them for a new password and call `updateUser()`:

        1

        supabase.auth.updateUser {

        2

            password = "1234567"

        3

        }


### Parameters

  * email

Required

String

The email to send the password reset email to.

  * redirectUrl

Optional

String?

The redirect url to use. If you don't specify this, the platform specific will be used, like deeplinks on android.

  * captchaToken

Optional

String?

The captcha token when having captcha enabled.


Send password reset email


    1

    supabase.auth.resetPasswordForEmail(email = "example@email.com")

* * *

## Verify and log in through OTP

  * Verifying an OTP is done through either `verifyPhoneOtp` or `verifyEmailOtp`.
  * The verification type used should be determined based on the corresponding auth method called before using `verifyPhoneOtp`/`verifyEmailOtp` to sign up / sign-in a user.


### Parameters

  * type

Required

OtpType.Email or OtpType.Phone

The OTP type. Depending on the type, an email or phone has to be specified as parameter.

  * email/phone

Required

String

The email or phone number, depending on which type you specified.

  * token

Required

String

The token to verify.

  * captchaToken

Optional

String?

The captcha token when having captcha enabled.


Verify an Email OTPVerify an Phone OTP


    1

    supabase.auth.verifyEmailOtp(type = OtpType.Email.EMAIL, email = "example@email.com", token = "token")

Notes

* * *

## Retrieve a session

Returns the current session, or `null` if there is none.

Get the session data


    1

    val session = supabase.auth.currentSessionOrNull()

* * *

## Retrieve a new session

This method will refresh the session whether the current one is expired or not.

  * This is done automatically, but can be disabled in the Auth config.


### Parameters

  * refreshToken

Required

String

The refresh token to use.


Refresh current sessionRefresh session using the refresh token


    1

    val session = supabase.auth.refreshCurrentSession()

* * *

## Retrieve a user

  * This method gets the user object from the current session.
  * Fetches the user object from the database instead of local session.
  * Should be used only when you require the most current user data. For faster results, `getCurrentSessionOrNull()?.user` is recommended.


### Parameters

  * jwt

Required

String

The JWT token.


Get the logged in user with the current sessionGet a user based on their access token


    1

    val user = supabase.auth.retrieveUserForCurrentSession(updateSession = true)

Notes

* * *

## Update a user

Modifies the user data.

  * In order to use the `updateUser()` method, the user needs to be signed in first.
  * By default, email updates sends a confirmation link to both the user's current and new email. To only send a confirmation link to the user's new email, disable **Secure email change** in your project's [email auth provider settings](https://supabase.com/dashboard/project/_/auth/providers).


### Parameters

  * updateCurrentUser

Optional

Boolean

Whether to update the local session with the new user. Defaults to `true`.

  * redirectUrl

Optional

String?

The redirect url to use. If you don't specify this, the platform specific will be used, like deeplinks on android.

  * config

Required

UserUpdateBuilder.() -> Unit

Details


Update the email for an authenticated userUpdate the password for an authenticated userUpdate the user's metadata


    1

    val user = supabase.auth.updateUser {

    2

       email = "newEmail@email.com"

    3

    }

Notes

* * *

## Retrieve identities linked to a user

  * The user needs to be signed in to call `currentIdentitiesOrNull()`.


Returns a list of identities linked to the user


    1

    //get the identities from the current user

    2

    val identities = supabase.auth.currentIdentitiesOrNull()

    3

    //Or retrieve them

    4

    val identities = supabase.auth.retrieveUserForCurrentSession().identities

* * *

## Link an identity to a user

  * The **Enable Manual Linking** option must be enabled from your [project's authentication settings](/dashboard/project/_/auth/providers).
  * The user needs to be signed in to call `linkIdentity()`.
  * If the candidate identity is already linked to the existing user or another user, `linkIdentity()` will fail.
  * This method works similarly to `signInWith()` using an OAuthProvider. To learn how to handle OTP links & OAuth refer to [initializing](/docs/reference/kotlin/initializing)


### Parameters

  * provider

Required

OAuthProvider

The OAuth provider you want to link the user with.

  * redirectUrl

Optional

String?

The redirect url to use. If you don't specify this, the platform specific will be used, like deeplinks on android.

  * config

Optional

ExternalAuthConfigDefaults.() -> Unit

Extra configuration.

Details


Link an identity to a userLink an identity to a user via an id token


    1

    supabase.auth.linkIdentity(OAuthProvider)

    2

    3

    //Example:

    4

    supabase.auth.linkIdentity(Google)

* * *

## Unlink an identity from a user

  * The **Enable Manual Linking** option must be enabled from your [project's authentication settings](/dashboard/project/_/auth/providers).
  * The user needs to be signed in to call `unlinkIdentity()`.
  * The user must have at least 2 identities in order to unlink an identity.
  * The identity to be unlinked must belong to the user.


### Parameters

  * identityId

Required

String

The id of the OAuth identity

  * updateLocalUser

Optional

Boolean

Whether to delete the identity from the local user or not. Defaults to `true`.


Unlink an identity


    1

    //get all identities linked to a user

    2

    val identities = supabase.auth.currentIdentitiesOrNull() ?: emptyList()

    3

    4

    //find the google identity linked to the user

    5

    val googleIdentity = identities.first { it.provider == "google" }

    6

    7

    //unlink the google identity from the user

    8

    supabase.auth.unlinkIdentity(googleIdentity.identityId!!)

* * *

## Send a password reauthentication nonce

  * This method is used together with `updateUser()` when a user's password needs to be updated.
  * This method will send a nonce to the user's email. If the user doesn't have a confirmed email address, the method will send the nonce to the user's confirmed phone number instead.


Send reauthentication nonce


    1

    supabase.auth.reauthenticate()

Notes

* * *

## Resend an OTP

  * Resends a signup confirmation, email change or phone change email to the user.
  * Passwordless sign-ins can be resent by calling the `signInWith(OTP)` method again.
  * Password recovery emails can be resent by calling the `resetPasswordForEmail()` method again.
  * This method will only resend an email or phone OTP to the user if there was an initial signup, email change or phone change request being made.


### Parameters

  * type

Required

OtpType.Email or OtpType.Phone

The OTP type. Depending on the type, an email or phone has to be specified as parameter.

  * email/phone

Required

String

The email or phone number, depending on which type you specified.

  * captchaToken

Optional

String?

The captcha token when having captcha enabled.


Resend an email signup confirmationResend a phone signup confirmationResend email change emailResend phone change OTP


    1

    supabase.auth.resendEmail(OtpType.Email.SIGNUP, "example@email.com")

Notes

* * *

## Set the session data

Changes the local session.

  * `importSession()` takes in a UserSession.
  * [Refresh token rotation](/docs/reference/auth/config#refresh_token_rotation_enabled) is enabled by default on all projects to guard against replay attacks.
  * You can configure the [`REFRESH_TOKEN_REUSE_INTERVAL`](https://supabase.com/docs/reference/auth/config#refresh_token_reuse_interval) which provides a short window in which the same refresh token can be used multiple times in the event of concurrency or offline issues.


### Parameters

  * session

Required

UserSession

The session to set.


Set local session


    1

    supabase.auth.importSession(UserSession(accessToken = "token", refreshToken = "refresh", expiresIn = 2000, tokenType = "Bearer", user = null))

Notes

* * *

## Exchange an auth code for a session

  * Used when `flowType` is set to `FlowType.PKCE` in the Auth configuration.


### Parameters

  * code

Required

String

The code to exchange.

  * saveSession

Optional

Boolean

Whether to save the session. Defaults to true.


Exchange Auth Code


    1

    supabase.auth.exchangeCodeForSession("34e770dd-9ff9-416c-87fa-43b31d7ef225")

* * *

## Auth MFA

This section contains methods commonly used for Multi-Factor Authentication (MFA) and are invoked behind the `supabase.auth.mfa` namespace.

Currently, we only support time-based one-time password (TOTP) as the 2nd factor. We don't support recovery codes but we allow users to enroll more than 1 TOTP factor, with an upper limit of 10.

Having a 2nd TOTP factor for recovery frees the user of the burden of having to store their recovery codes somewhere. It also reduces the attack surface since multiple recovery codes are usually generated compared to just having 1 backup TOTP factor.

* * *

## Enroll a factor

Enrolls a new factor.

  * Use `FactorType.TOTP` or `FactorType.Phone` as the factorType and use the returned id to create a challenge.
  * To create a challenge, see [`mfa.createChallenge()`](/docs/reference/kotlin/auth-mfa-challenge).
  * To verify a challenge, see [`mfa.verifyChallenge()`](/docs/reference/kotlin/auth-mfa-verify).
  * To create and verify a challenge in a single step, see [`mfa.createChallengeAndVerify()`](/docs/reference/kotlin/auth-mfa-challengeandverify).


### Parameters

  * factorType

Required

FactorType<C, R>

The type of MFA factor to enroll. Currently supports `FactorType.TOTP` and `FactorType.Phone`.

  * issuer

Optional

String?

Domain which the user is enrolling with.

  * config

Optional

Config.() -> Unit

Factor type specific configuration.


Enroll a time-based, one-time password (TOTP) factorEnroll a Phone FactorCheck the local user for verified factorsRetrieve verified factors


    1

    val factor = supabase.auth.mfa.enroll(factorType = FactorType.TOTP, friendlyName = "Your friendly Name") {

    2

          // Optional

    3

          issuer = "example.com"

    4

    }

    5

    6

    // Use the id to create a challenge.

    7

    // The challenge can be verified by entering the code generated from the authenticator app.

    8

    // The code will be generated upon scanning the qr_code or entering the secret into the authenticator app.

    9

    val (id, type, qrCode) = factor.data //qrCode is a svg as a string

    10

    val (factorId, factorType, _) = factor

    11

    val challenge = supabase.auth.mfa.createChallenge(factor.id)

* * *

## Create a challenge

Creates a challenge for a factor.

  * An [enrolled factor](/docs/reference/kotlin/auth-mfa-enroll) is required before creating a challenge.
  * To verify a challenge, see [`mfa.verifyChallenge()`](/docs/reference/kotlin/auth-mfa-verify).
  * A phone factor sends a code to the user upon challenge. The channel defaults to `Phone.Channel.SMS` unless otherwise specified.


### Parameters

  * factorId

Required

String

The id of the MFA factor you want to create a challenge for.

  * channel

Optional

Phone.Channel?

The channel to send the challenge to. Defaults to `Phone.Channel.SMS`.


Create a challenge for a factor


    1

    val challenge = supabase.auth.mfa.createChallenge(factorId = "34e770dd-9ff9-416c-87fa-43b31d7ef225")

* * *

## Verify a challenge

Verifies a challenge for a factor.

  * To verify a challenge, please [create a challenge](/docs/reference/kotlin/auth-mfa-challenge) first.


### Parameters

  * factorId

Required

String

The id of the MFA factor to verify.

  * challengeId

Required

String

The id of the challenge to verify.

  * code

Required

String

The code used to verify.

  * saveSession

Optional

Boolean

Whether to save the session. Defaults to true.


Verify a challenge for a factor


    1

    supabase.auth.mfa.verifyChallenge(

    2

        factorId = "34e770dd-9ff9-416c-87fa-43b31d7ef225",

    3

        challengeId = "4034ae6f-a8ce-4fb5-8ee5-69a5863a7c15",

    4

        code = "123456",

    5

        saveSession = true // this is set to true by default, but you can set it to false if you want to handle the session yourself

    6

    )

* * *

## Create and verify a challenge

Creates and verifies a challenge for a factor.

  * Creating and verifying a challenge in a single step is not supported by the `Phone` factor type.
  * An [enrolled factor](/docs/reference/kotlin/auth-mfa-enroll) is required before invoking `createChallengeAndVerify()`.
  * Executes [`mfa.createChallenge()`](/docs/reference/kotlin/auth-mfa-challenge) and [`mfa.verifyChallenge()`](/docs/reference/kotlin/auth-mfa-verify) in a single step.


### Parameters

  * factorId

Required

String

The id of the MFA factor to verify.

  * code

Required

String

The code used to verify.

  * saveSession

Optional

Boolean

Whether to save the session. Defaults to true.


Create and verify a challenge for a factor


    1

    supabase.auth.mfa.createChallengeAndVerify(

    2

        factorId = "34e770dd-9ff9-416c-87fa-43b31d7ef225",

    3

        code = "123456",

    4

        saveSession = true // this is set to true by default, but you can set it to false if you want to handle the session yourself

    5

    )

* * *

## Unenroll a factor

Unenroll removes a MFA factor. A user has to have an `AAL2` authentication level in order to unenroll a verified factor.

### Parameters

  * factorId

Required

String

The id of the factor you want to unenroll.


Unenroll a factor


    1

    supabase.auth.mfa.unenroll(factorId = "34e770dd-9ff9-416c-87fa-43b31d7ef225")

* * *

## Get Authenticator Assurance Level

  * Authenticator Assurance Level (AAL) is the measure of the strength of an authentication mechanism.
  * In Supabase, having an AAL of `aal1` refers to having the 1st factor of authentication such as an email and password or OAuth sign-in while `aal2` refers to the 2nd factor of authentication such as a time-based, one-time-password (TOTP).
  * If the user has a verified factor, the `next` field will return `AuthenticatorAssuranceLevel.AAL2`, else, it will return `AuthenticatorAssuranceLevel.AAL1`.


Get the AAL details of the current sessionCheck whether the user has at least one verified factorCheck whether the user is logged in using AAL2


    1

    val (current, next) = supabase.auth.mfa.getAuthenticatorAssuranceLevel()

* * *

## Auth Admin

  * Any method under the `supabase.auth.admin` namespace requires a `secret` key.
  * These methods are considered admin methods and should be called on a trusted server. Never expose your `secret` key in the browser.


Create server-side auth client


    1

    val supabase = createSupabaseClient(

    2

        supabaseUrl = "https://id.supabase.co",

    3

        supabaseKey = "supabaseKey"

    4

    ) {

    5

        install(Auth) {

    6

            minimalConfig() //disables session saving and auto-refreshing

    7

        }

    8

        // install other plugins (these will use the secret key)

    9

    }

    10

    supabase.auth.importAuthToken("your-secret-key")

    11

    12

    // Access auth admin api

    13

    val adminAuthClient = supabase.auth.admin

* * *

## Retrieve a user

Fetches the user object from the database based on the user's id.

  * The `retrieveUserById()` method requires the user's id which maps to the `auth.users.id` column.


### Parameters

  * uid

Required

String

The id of the user you want to retrieve.


Fetch the user object using the access_token jwt


    1

    val user = supabase.auth.admin.retrieveUserById(uid = "f2a0b0a0-6b1a-4b7a-8f1a-4b7a6b1a8f1a")

* * *

## List all users

Retrieves a list of users.

  * Defaults to return 50 users per page.


### Parameters

  * page

Optional

Int

The page number to retrieve.

  * perPage

Optional

Int

The number of users to retrieve per page.


Get a page of usersPaginated list of users


    1

    val users = supabase.auth.admin.retrieveUsers()

* * *

## Create a user

Creates a new user.

  * To confirm the user's email address or phone number, set `autoConfirm` to true. Both arguments default to false.


### Parameters

  * builder

Required

AdminUserBuilder.Email.() -> Unit or AdminUserBuilder.Phone.() -> Unit

The builder to create a new user.

Details


Create user with emailCreate user with phoneAuto-confirm the user's emailAuto-confirm the user's phone number


    1

    val userWithEmail = supabase.auth.admin.createUserWithEmail {

    2

        email = "example@email.com"

    3

        password = "secretpassword"

    4

        userMetadata {

    5

            put("name", "John")

    6

        }

    7

    }

* * *

## Delete a user

Deletes a user from the database.

  * The `deleteUser()` method requires the user's ID, which maps to the `auth.users.id` column.


### Parameters

  * uid

Required

String

The id of the user you want to delete.


Removes a user


    1

    supabase.auth.admin.deleteUser(uid = "uid")

* * *

## Send an email invite link

Sends an invite link to the user's email address.

### Parameters

  * email

Required

String

The email to send the invite to.

  * redirectTo

Optional

String

The redirect url to use. If you don't specify this, the platform specific will be used, like deeplinks on android.

  * data

Optional

JsonObject

Custom data to create the user with.


Invite a user


    1

    supabase.auth.admin.inviteUserByEmail(

    2

        email = "example@email.com",

    3

        //optional:

    4

        redirectTo = "https://example.com/redirect",

    5

        data = buildJsonObject {

    6

            put("custom", "value")

    7

        }

    8

    )

* * *

## Generate an email link

Generates email links and OTPs. This will not send links or OTPs to the end user. This function is for custom admin functionality.

### Parameters

  * type

Required

LinkType<C>

The type of link to generate, e.g. `LinkType.Signup`.

  * redirectTo

Optional

String

The redirect url to use. If you don't specify this, the platform specific will be used, like deeplinks on android.

  * config

Optional

C.() -> Unit

The builder to create a new link.


Generate a signup linkGenerate an invite linkGenerate a magic linkGenerate a recovery linkGenerate links to change current email address


    1

    val (url, user) = supabase.auth.admin.generateLinkFor(LinkType.Signup) {

    2

        email = "example@email.com"

    3

        password = "secretpassword"

    4

    }

* * *

## Update a user

Updates the user data.

### Parameters

  * uid

Required

String

The id of the user you want to update.

  * builder

Required

AdminUserUpdateBuilder.() -> Unit

The builder to update the user.

Details


Updates a user's emailUpdates a user's passwordUpdates a user's metadataUpdates a user's app_metadataConfirms a user's email addressConfirms a user's phone number


    1

    supabase.auth.admin.updateUserById(uid = "id") {

    2

        email = "example@email.com"

    3

    }

* * *

## Delete a factor for a user

Deletes a factor on a user. This will log the user out of all active sessions if the deleted factor was verified.

### Parameters

  * uid

Required

String

The id of the user you want to delete a factor for.

  * factorId

Required

String

The id of the factor you want to delete.


Delete a factor for a user


    1

    supabase.auth.admin.deleteFactor(uid = "id", factorId = "factor_id")

* * *

## OAuth Admin

The OAuth Admin API allows you to manage OAuth clients programmatically. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth. These functions should only be called on a server. Never expose your `secret key` in the browser.

* * *

## List OAuth clients

Lists all OAuth 2.1 clients. Requires a `secret key`.

### Parameters

  * page

Optional

Int?

The page number for pagination.

  * perPage

Optional

Int?

The number of results per page.


List all OAuth clients


    1

    val clients = supabase.auth.admin.oauth.listClients()

* * *

## Create OAuth client

Creates a new OAuth 2.1 client. Requires a `secret key`.

Create an OAuth client


    1

    val client = supabase.auth.admin.oauth.createClient {

    2

        name = "My App"

    3

        redirectUris = listOf("https://example.com/callback")

    4

    }

* * *

## Get OAuth client

Retrieves an OAuth 2.1 client by ID. Requires a `secret key`.

### Parameters

  * clientId

Required

String

The ID of the OAuth client to retrieve.


Get an OAuth client


    1

    val client = supabase.auth.admin.oauth.getClient(clientId = "client_id")

* * *

## Update OAuth client

Updates an existing OAuth 2.1 client. Requires a `secret key`.

### Parameters

  * clientId

Required

String

The ID of the OAuth client to update.


Update an OAuth client


    1

    val client = supabase.auth.admin.oauth.updateClient(clientId = "client_id") {

    2

        name = "Updated App Name"

    3

    }

* * *

## Delete OAuth client

Deletes an OAuth 2.1 client. Requires a `secret key`.

### Parameters

  * clientId

Required

String

The ID of the OAuth client to delete.


Delete an OAuth client


    1

    supabase.auth.admin.oauth.deleteClient(clientId = "client_id")

* * *

## Regenerate client secret

Regenerates the client secret for an OAuth 2.1 client. Requires a `secret key`.

### Parameters

  * clientId

Required

String

The ID of the OAuth client whose secret should be regenerated.


Regenerate client secret


    1

    val client = supabase.auth.admin.oauth.regenerateClientSecret(clientId = "client_id")

* * *

## Invokes a Supabase Edge Function.

Invokes a Supabase Function. See the [guide](/docs/guides/functions) for details on writing Functions.

  * When invoking a function with parameters, you have to provide a [serializable value](/docs/reference/kotlin/installing#serialization) in the function parameter.

  * Requires an Authorization header.


### Parameters

  * function

Required

String

The name of the function to invoke.

  * body

Optional

T

The body to send with the request. T can be any serializable type.

  * region

Optional

FunctionRegion

The region where the function is invoked. Defaults to `Functions.Config#defaultRegion`.

  * headers

Optional

Headers

The headers to send with the request.


Basic invocationBasic invocation with bodyInvoke with streaming (SSE)Reuse function by saving it to a variable


    1

    val response = supabase.functions.invoke("function_name")

    2

    3

    // Decode the response body to a serializable class

    4

    val data = response.body<FunctionResponse>()

* * *

## Listen to database changes

Return real-time data from your table as a [Flow](https://kotlinlang.org/docs/flow.html).

  * Realtime is disabled by default for new tables. You can turn it on by [managing replication](/docs/guides/realtime/postgres-changes#replication-setup).
  * `selectAsFlow` and `selectSingleValueAsFlow` will emit the initial data and then listen for changes.
  * Takes in a `filter` parameter to filter the data and a `primaryKey` parameter to cache the data by the primary key.
  * This method requires both the `Realtime` and `Postgrest` plugins to be installed.
  * The type parameter `T` must be a [serializable class](/docs/reference/kotlin/installing#serialization).
  * If you want more control over the realtime updates, you can use the `Realtime` plugin directly.


### Parameters

  * primaryKey

Required

KProperty1<Data, Value> or PrimaryKey<Data>

The primary key to cache the data by. Can be a property reference or a custom primary key.

  * channelName

Optional

String

The name of the channel to use for the realtime updates. If null, a channel name following the format "schema:table:id" will be used

  * filter

Optional

PostgrestFilterBuilder.() -> Unit or FilterOperation

The filter to apply to the data.


Listen for changes in multiple rowsListen for changes in multiple rows with a filterListen for changes in a single row


    1

    val flow: Flow<List<Country>> = supabase.from("countries").selectAsFlow(Country::id)

    2

    flow.collect {

    3

        for (country in it) {

    4

            println(country.name)

    5

        }

    6

    }

* * *

## Subscribe to channel

Subscribe to realtime changes in your database.

  * Realtime is disabled by default for new Projects for better database performance and security. You can turn it on by [managing replication](/docs/guides/database/api#managing-realtime).
  * If you want to receive the "previous" data for updates and deletes, you will need to set `REPLICA IDENTITY` to `FULL`, like this: `ALTER TABLE your_table REPLICA IDENTITY FULL;`
  * When using a method with a generic type like `track`, `broadcast` or `broadcastFlow`, you have to provide a [serializable class](/docs/reference/kotlin/installing#serialization) as the type parameter.
  * Presence, Broadcast and Database updates are sent through a [Flow](https://kotlinlang.org/docs/flow.html)


Listen to broadcastsListen to presence updatesListen to all database changesListen to a specific tableListen to insertsListen to updatesListen to deletesListen to row level changes


    1

    @Serializable

    2

    data class Message(val content: String, val sender: String)

    3

    4

    val channel = supabase.channel("channelId") {

    5

        // optional config

    6

    }

    7

    8

    val broadcastFlow = channel.broadcastFlow<Message>(event = "message")

    9

    10

    // Collect the flow

    11

    broadcastFlow.onEach { // it: Message

    12

        println(it)

    13

    }.launchIn(coroutineScope) // launch a new coroutine to collect the flow

    14

    15

    channel.subscribe(blockUntilSubscribed = true)

* * *

## Unsubscribe from a channel

Unsubscribes and removes Realtime channel from Realtime client.

  * Removing a channel is a great way to maintain the performance of your project's Realtime service as well as your database if you're listening to Postgres changes.
  * Supabase will automatically handle cleanup 30 seconds after a client is disconnected, but unused channels may cause degradation as more clients are simultaneously subscribed.
  * If you removed all channels, the client automatically disconnects from the Realtime websocket. This can be disabled in the Realtime config by setting `disconnectOnNoSubscriptions` to false.


Remove a channelUnsubscribe from a channel


    1

    val channel = supabase.channel("channelId") {

    2

        //optional config

    3

    }

    4

    //...

    5

    supabase.realtime.removeChannel(channel)

* * *

## Unsubscribe from all channels

Unsubscribes and removes all Realtime channels from Realtime client.

  * Removing channels is a great way to maintain the performance of your project's Realtime service as well as your database if you're listening to Postgres changes. Supabase will automatically handle cleanup 30 seconds after a client is disconnected, but unused channels may cause degradation as more clients are simultaneously subscribed.
  * If you removed all channels, the client automatically disconnects from the Realtime websocket. This can be disabled in the Realtime config by setting `disconnectOnNoSubscriptions` to false.


Remove all channels


    1

    supabase.realtime.removeAllChannels()

* * *

## Retrieve all channels

Returns all Realtime channels.

Get all channels


    1

    val channels = supabase.realtime.subscriptions.entries

* * *

## Broadcast a message

Broadcast a message to all connected clients to a channel.

  * When using REST you don't need to subscribe to the channel


Send a message via websocketSend a message via REST


    1

    val channel = supabase.channel("room1")

    2

    channel.subscribe(blockUntilSubscribed = true)

    3

    channel.broadcast("cursor-pos", message = buildJsonObject {

    4

      put("x", 10)

    5

      put("y", 20)

    6

    })

Response

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

    val buckets = supabase.storage.retrieveBuckets()

* * *

## Retrieve a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: `select`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


Get bucket


    1

    val bucket = supabase.storage.retrieveBucketById(bucketId = "avatars")

* * *

## Create a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: `insert`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * id

Required

String

The id of the bucket you want to create.

  * builder

Optional

BucketBuilder.() -> Unit

The builder to create a new bucket.

Details


Create bucket


    1

    supabase.storage.createBucket(id = "icons") {

    2

        public = true

    3

        fileSizeLimit = 5.megabytes

    4

    }

* * *

## Empty a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: `select`
    * `objects` table permissions: `select` and `delete`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * bucketId

Required

String

The id of the bucket you want to empty.


Empty bucket


    1

    supabase.storage.emptyBucket(bucketId = "icons")

* * *

## Update a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: `select` and `update`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * id

Required

String

The id of the bucket you want to create.

  * builder

Optional

BucketBuilder.() -> Unit

The builder to create a new bucket.

Details


Update bucket


    1

    supabase.storage.updateBucket("cards") {

    2

        public = false

    3

        fileSizeLimit = 20.megabytes

    4

        allowedMimeTypes(ContentType.Image.PNG, ContentType.Image.JPEG)

    5

    }

* * *

## Delete a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: `select` and `delete`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * bucketId

Required

String

The id of the bucket you want to delete.


Delete bucket


    1

    supabase.storage.deleteBucket(bucketId = "icons")

* * *

## Upload a file

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `insert`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works
  * Resumable uploads use a `Disk` cache by default to store the upload urls. You can customize that in the Auth config by changing the `resumable.cache` property.


### Parameters

  * path

Required

String

The path of the file you want to upload.

  * data

Required

ByteArray

The data of the file you want to upload.

  * options

Optional

UploadOptionBuilder.() -> Unit

Additional options for the upload.

Details


Upload fileUpload file with progressCreate resumable uploadStart and resumable uploadPause resumable uploadCancel resumable uploadListen to the resumable upload stateContinue previous uploads


    1

    val bucket = supabase.storage.from("avatars")

    2

    bucket.upload("myIcon.png", byteArray) {

    3

        upsert = false

    4

    }

    5

    //on JVM you can use java.io.File

    6

    bucket.upload("myIcon.png", file) {

    7

        upsert = false

    8

    }

* * *

## Replace an existing file

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `update` and `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * path

Required

String

The path of the file you want to upload.

  * data

Required

ByteArray

The data of the file you want to upload.

  * options

Optional

UploadOptionBuilder.() -> Unit

Additional options for the upload.

Details


Update file


    1

    val bucket = supabase.storage.from("avatars")

    2

    bucket.update("myIcon.png", byteArray) {

    3

        upsert = false

    4

    }

    5

    //on JVM you can use java.io.File

    6

    bucket.update("myIcon.png", file) {

    7

        upsert = false

    8

    }

* * *

## Move an existing file

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `update` and `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * from

Required

String

The path of the file you want to move.

  * to

Required

String

The new path of the file.

  * destinationBucket

Optional

String

The destination bucket of the file.


Move file


    1

    val bucket = supabase.storage.from("avatars")

    2

    bucket.move("icon1.png", "icon2.png")

* * *

## Copy an existing file

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `insert` and `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * from

Required

String

The path of the file you want to copy.

  * to

Required

String

The new path of the file.

  * destinationBucket

Optional

String

The destination bucket of the file.


Copy file


    1

    supabase.storage.from("test").copy(from = "avatar.png", to = "avatar2.png")

* * *

## Create a signed URL

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * path

Required

String

The path of the file you want to create a signed url for.

  * expiresIn

Required

Duration

The duration the signed url should be valid for.

  * builder

Optional

ImageTransformation.() -> Unit

The transformation to apply to the image.

Details


Create Signed URLCreate Signed URL with transformation


    1

    val bucket = supabase.storage.from("avatars")

    2

    val url = bucket.createSignedUrl(path = "icon.png", expiresIn = 3.minutes)

* * *

## Create signed URLs

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * expiresIn

Required

Duration

The duration the signed url should be valid for.

  * paths

Required

vararg String

The paths of the files you want to create signed urls for.


Create Signed URLs


    1

    val urls = supabase.storage.from("avatars").createSignedUrls(20.minutes, "avata1.jpg", "avatar2.jpg")

* * *

## Create signed upload URL

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `insert`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * path

Required

String

The path of the file you want to upload.

  * upsert

Optional

Boolean

Whether to overwrite the file if it already exists.


Create Signed Upload URL


    1

    val url = supabase.storage.from("avatars").createSignedUploadUrl("avatar.png")

* * *

## Upload to a signed URL

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * path

Required

String

The path of the file you want to upload.

  * token

Required

String

The token you received from `createSignedUploadUrl`.

  * data

Optional

ByteArray

The data of the file you want to upload.

  * options

Optional

UploadOptionBuilder.() -> Unit

Additional options for the upload.

Details


Upload to a signed URL


    1

    supabase.storage.from("avatars").uploadToSignedUrl(path = "avatar.jpg", token = "token-from-createSignedUploadUrl", data = bytes)

    2

    //or on JVM:

    3

    supabase.storage.from("avatars").uploadToSignedUrl(path = "avatar.jpg", token = "token-from-createSignedUploadUrl", file = File("avatar.jpg"))

* * *

## Retrieve public URL

  * The bucket needs to be set to public, either via [updateBucket()](/docs/reference/kotlin/storage-updatebucket) or by going to Storage on [supabase.com/dashboard](https://supabase.com/dashboard), clicking the overflow menu on a bucket and choosing "Make public"
  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * path

Required

String

The path of the file you want to get the public url for.


Returns the URL for an asset in a public bucketReturns the URL for an asset in a public bucket with transformations


    1

    val url = supabase.storage.from("public-bucket").publicUrl("folder/avatar1.png")

* * *

## Download a file

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * path

Required

String

The path of the file you want to download.

  * options

Optional

DownloadOptionBuilder.() -> Unit

Additional options for the download.

Details


Download file from non-public bucketDownload file from public bucketDownload file with transformationDownload file with progress


    1

    val bucket = supabase.storage.from("avatars")

    2

    val bytes = bucket.downloadAuthenticated("test.png")

    3

    //or on JVM:

    4

    bucket.downloadAuthenticatedTo("test.png", File("test.png"))

* * *

## Delete files in a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `delete` and `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * paths

Required

vararg String

The paths of the files you want to remove.


Delete file


    1

    val bucket = supabase.storage.from("avatars")

    2

    bucket.delete("test.png", "test2.png")

* * *

## List all files in a bucket

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


List files in a bucket


    1

    val bucket = supabase.storage.from("avatars")

    2

    val files = bucket.list()

* * *

## Check if file exists

Checks the existence of a file.

### Parameters

  * path

Required

String

The file path, including the file name. For example `folder/image.png`.


Check file existence


    1

    val exists = supabase.storage.from("avatars").exists("folder/avatar1.png")

* * *

## Get file metadata

Retrieves the details of an existing file. Returns detailed file metadata including size, content type, and timestamps.

### Parameters

  * path

Required

String

The file path, including the file name. For example `folder/image.png`.


Get file info


    1

    val file = supabase.storage.from("avatars").info("folder/avatar1.png")

    2

    println(file.size)

* * *

## Analytics Buckets

This section contains methods for working with Analytics Buckets.

  * Analytics buckets are accessed via the `supabase.storage.analytics` namespace.
  * Analytics buckets are used for storing and querying analytics data.


* * *

## Create a new analytics bucket

Creates a new Analytics bucket.

  * This is an alpha feature and may change in the future.
  * Analytics buckets are used for storing and querying analytics data.


### Parameters

  * name

Required

String

The name of the analytics bucket to create.


Create analytics bucket


    1

    val bucket = supabase.storage.analytics.createBucket("analytics-bucket")

Response

* * *

## List analytics buckets

Lists all Analytics buckets with optional filtering and sorting.

  * This is an alpha feature and may change in the future.


### Parameters

  * filter

Optional

StorageListFilter.Buckets.() -> Unit

Filter block for the operation.

Details


List analytics buckets


    1

    val buckets = supabase.storage.analytics.listBuckets {

    2

        limit = 50

    3

        offset = 2

    4

        search = "myString"

    5

        sortBy(BucketSortColumn.ID, SortOrder.ASC)

    6

    }

Response

* * *

## Delete an analytics bucket

Deletes an Analytics bucket.

  * This is an alpha feature and may change in the future.


### Parameters

  * name

Required

String

The name of the analytics bucket to delete.


Delete analytics bucket


    1

    val responseMessage = supabase.storage.analytics.deleteBucket("analytics-bucket")

Response

* * *

## Vector Buckets

This section contains methods for working with Vector Buckets.

* * *

## Create a vector bucket

Creates a new Vector bucket.

  * This is an alpha feature and may change in the future.
  * Vector buckets are used for storing and querying vector embeddings.


### Parameters

  * name

Required

String

The name of the vector bucket to create.


Create vector bucket


    1

    supabase.storage.vectors.createBucket("vectors-bucket")

* * *

## Delete a vector bucket

Deletes a Vector bucket.

  * This is an alpha feature and may change in the future.


### Parameters

  * name

Required

String

The name of the vector bucket to delete.


Delete vector bucket


    1

    supabase.storage.vectors.deleteBucket("vectors-bucket")

* * *

## Retrieve a vector bucket

Retrieves details of a Vector bucket.

  * This is an alpha feature and may change in the future.


### Parameters

  * name

Required

String

The name of the vector bucket to retrieve.


Get vector bucket


    1

    val bucket = supabase.storage.vectors.getBucket("vectors-bucket")

Response

* * *

## List all vector buckets

Lists all Vector buckets with optional pagination.

  * This is an alpha feature and may change in the future.


### Parameters

  * filter

Optional

VectorBucketFilter.() -> Unit

Optional filter block for the operation.

Details


List vector buckets


    1

    val response = supabase.storage.vectors.listBuckets {

    2

        prefix = "myPrefix"

    3

        maxResults = 20

    4

        nextToken = "nextToken"

    5

    }

    6

    println(response.vectorBuckets)

Response

* * *

## Create a vector index

Creates a new vector index in a vector bucket.

  * This is an alpha feature and may change in the future.


### Parameters

  * options

Required

CreateIndexOptions.() -> Unit

Configuration block for the operation.

Details


Create vector index


    1

    supabase.storage.vectors

    2

      .from("vectors-bucket")

    3

      .createIndex {

    4

        indexName = "my-index"

    5

        dimension = 128

    6

        distanceMetric = DistanceMetric.COSINE

    7

        dataType = VectorDataType.FLOAT32

    8

      }

* * *

## Delete a vector index

Deletes a vector index.

  * This is an alpha feature and may change in the future.


### Parameters

  * indexName

Required

String

The name of the index to delete.


Delete vector index


    1

    supabase.storage.vectors

    2

      .from("vectors-bucket")

    3

      .deleteIndex("index-name")

* * *

## Retrieve a vector index

Retrieves details of a vector index.

  * This is an alpha feature and may change in the future.


### Parameters

  * indexName

Required

String

The name of the index to retrieve.


Get vector index


    1

    val index = supabase.storage.vectors

    2

        .from("vectors-bucket")

    3

        .getIndex("index-name")

* * *

## List all vector indexes

Lists all indexes in a vector bucket.

  * This is an alpha feature and may change in the future.


### Parameters

  * options

Required

ListIndexesOptions.() -> Unit

Configuration block for the operation.

Details


List vector indexes


    1

    val response = supabase.storage.vectors

    2

      .from("vectors-bucket")

    3

      .listIndexes {

    4

          maxResults = 20

    5

          prefix = "myPrefix"

    6

          nextToken = "nextToken"

    7

      }

    8

    println(response.indexes)

* * *

## Delete vectors from index

Deletes vectors by their keys.

  * This is an alpha feature and may change in the future.
  * Keys batch size must be between 1 and 500.


### Parameters

  * bucketName

Required

String

The name of the vector bucket.

  * indexName

Required

String

The name of the index.

  * keys

Required

List<String>

List of vector keys to delete. Must contain between 1 and 500 keys.


Delete vectors


    1

    supabase.storage.vectors

    2

        .from("vectors-bucket")

    3

        .index("index-name")

    4

        .deleteVectors(listOf("vector-1", "vector-2"))

* * *

## Retrieve vectors from index

Retrieves vectors by their keys.

  * This is an alpha feature and may change in the future.


### Parameters

  * options

Required

GetVectorOptions.() -> Unit

Configuration block for the operation.

Details


Get vectors


    1

    val vectors = supabase.storage.vectors

    2

        .from("vectors-bucket")

    3

        .index("index-name")

    4

        .getVectors {

    5

            keys.add("vector-1")

    6

        }

* * *

## List vectors in index

Lists vectors in an index with optional pagination.

  * This is an alpha feature and may change in the future.


### Parameters

  * options

Optional

ListVectorsOptions.() -> Unit

Optional configuration block for the operation. If omitted, default pagination and response settings are used.

Details


List vectors


    1

    val response = supabase.storage.vectors

    2

      .from("vectors-bucket")

    3

      .index("index-name")

    4

      .listVectors()

    5

    println(response.vectors)

* * *

## Add vectors to index

Inserts or updates vectors in an index.

  * This is an alpha feature and may change in the future.


### Parameters

  * vectors

Required

List<VectorObject>

List of vector objects to insert or update.


Put vectors


    1

    supabase.storage.vectors

    2

      .from("vectors-bucket")

    3

      .index("index-name")

    4

      .putVectors(listOf(

    5

          VectorObject("vector-1", VectorData(floatArrayOf(2.0f, 3.0f, 4.0f)), buildJsonObject { put("meta", true) })

    6

      ))

* * *

## Search vectors in index

Queries vectors using a query vector to find similar vectors.

  * This is an alpha feature and may change in the future.


### Parameters

  * options

Required

QueryVectorsOptions.() -> Unit

Configuration block for the query.

Details


Query vectors


    1

    val response = supabase.storage.vectors

    2

        .from("vectors-bucket")

    3

        .index("index-name")

    4

        .queryVectors {

    5

            queryVector = VectorData(floatArrayOf(3.0f, 4.0f, 1.0f))

    6

            topK = 10

    7

        }

    8

    println(response.vectors)