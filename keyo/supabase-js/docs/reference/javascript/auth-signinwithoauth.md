---
title: "JavaScriptClient Library"
url: https://supabase.com/docs/reference/javascript/auth-signinwithoauth
---

# JavaScriptClient Library

JavaScript Reference v2.0

# JavaScript Client Library

@supabase/supabase-js[View on GitHub](https://github.com/supabase/supabase-js)

This reference documents every object and method available in Supabase's isomorphic JavaScript library, `supabase-js`. You can use `supabase-js` to interact with your Postgres database, listen to database changes, invoke Deno Edge Functions, build login and user management functionality, and manage large files.

To convert SQL queries to `supabase-js` calls, use the [SQL to REST API translator](/docs/guides/api/sql-to-rest).

* * *

## Installing

### Install as package#

You can install @supabase/supabase-js via the terminal.

npmYarnpnpm


    1

    npm install @supabase/supabase-js

### Install via CDN#

You can install @supabase/supabase-js via CDN links.


    1

    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>

    2

    //or

    3

    <script src="https://unpkg.com/@supabase/supabase-js@2"></script>

### Use at runtime in Deno#

You can use supabase-js in the Deno runtime via [JSR](https://jsr.io/@supabase/supabase-js):


    1

    import { createClient } from 'npm:@supabase/supabase-js@2'

### Enable Data API access#

supabase-js uses the Data API to query and mutate your Postgres data. You first need to grant Data API roles permissions to access your tables and functions.

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

Create a new client for use in the browser.

### Parameters

  * supabaseUrlstring

The unique Supabase URL which is supplied when you create a new project in your project dashboard.

  * supabaseKeystring

The unique Supabase Key which is supplied when you create a new project in your project dashboard.

  * options

Optional

SupabaseClientOptions

Details


Creating a clientWith a custom domainWith additional parametersWith custom schemasCustom fetch implementationReact Native options with AsyncStorageReact Native options with Expo SecureStoreWith a database queryWith OpenTelemetry tracing


    1

    import { createClient } from '@supabase/supabase-js'

    2

    3

    // Create a single supabase client for interacting with your database

    4

    const supabase = createClient('https://xyzcompany.supabase.co', 'your-publishable-key')

* * *

## TypeScript support

`supabase-js` has TypeScript support for type inference, autocompletion, type-safe queries, and more.

With TypeScript, `supabase-js` detects things like `not null` constraints and [generated columns](https://www.postgresql.org/docs/current/ddl-generated-columns.html). Nullable columns are typed as `T | null` when you select the column. Generated columns will show a type error when you insert to it.

`supabase-js` also detects relationships between tables. A referenced table with one-to-many relationship is typed as `T[]`. Likewise, a referenced table with many-to-one relationship is typed as `T | null`.

## Generating TypeScript Types#

You can use the Supabase CLI to [generate the types](/docs/reference/cli/supabase-gen-types). You can also generate the types [from the dashboard](https://supabase.com/dashboard/project/_/api?page=tables-intro).


    1

    supabase gen types typescript --project-id abcdefghijklmnopqrst > database.types.ts

These types are generated from your database schema. Given a table `public.movies`, the generated types will look like:


    1

    create table public.movies (

    2

      id bigint generated always as identity primary key,

    3

      name text not null,

    4

      data jsonb null

    5

    );


    1

    export type Json = string | number | boolean | null | { [key: string]: Json | undefined } | Json[]

    2

    3

    export interface Database {

    4

      public: {

    5

        Tables: {

    6

          movies: {

    7

            Row: {               // the data expected from .select()

    8

              id: number

    9

              name: string

    10

              data: Json | null

    11

            }

    12

            Insert: {            // the data to be passed to .insert()

    13

              id?: never         // generated columns must not be supplied

    14

              name: string       // `not null` columns with no default must be supplied

    15

              data?: Json | null // nullable columns can be omitted

    16

            }

    17

            Update: {            // the data to be passed to .update()

    18

              id?: never

    19

              name?: string      // `not null` columns are optional on .update()

    20

              data?: Json | null

    21

            }

    22

          }

    23

        }

    24

      }

    25

    }

## Using TypeScript type definitions#

You can supply the type definitions to `supabase-js` like so:


    1

    import { createClient } from '@supabase/supabase-js'

    2

    import { Database } from './database.types'

    3

    4

    const supabase = createClient<Database>(

    5

      process.env.SUPABASE_URL,

    6

      process.env.SUPABASE_PUBLISHABLE_KEY

    7

    )

## Helper types for Tables and Joins#

You can use the following helper types to make the generated TypeScript types easier to use.

Sometimes the generated types are not what you expect. For example, a view's column may show up as nullable when you expect it to be `not null`. Using [type-fest](https://github.com/sindresorhus/type-fest), you can override the types like so:


    1

    export type Json = // ...

    2

    3

    export interface Database {

    4

      // ...

    5

    }


    1

    import { MergeDeep } from 'type-fest'

    2

    import { Database as DatabaseGenerated } from './database-generated.types'

    3

    export { Json } from './database-generated.types'

    4

    5

    // Override the type for a specific column in a view:

    6

    export type Database = MergeDeep<

    7

      DatabaseGenerated,

    8

      {

    9

        public: {

    10

          Views: {

    11

            movies_view: {

    12

              Row: {

    13

                // id is a primary key in public.movies, so it must be `not null`

    14

                id: number

    15

              }

    16

            }

    17

          }

    18

        }

    19

      }

    20

    >

You can also override the type of an individual successful response if needed:


    1

    // Partial type override allows you to only override some of the properties in your results

    2

    const { data } = await supabase.from('countries').select().overrideTypes<Array<{ id: string }>>()

    3

    // For a full replacement of the original return type use the `{ merge: false }` property as second argument

    4

    const { data } = await supabase

    5

      .from('countries')

    6

      .select()

    7

      .overrideTypes<Array<{ id: string }>, { merge: false }>()

    8

    // Use it with `maybeSingle` or `single`

    9

    const { data } = await supabase.from('countries').select().single().overrideTypes<{ id: string }>()

The generated types provide shorthands for accessing tables and enums.


    1

    import { Database, Tables, Enums } from "./database.types.ts";

    2

    3

    // Before 😕

    4

    let movie: Database['public']['Tables']['movies']['Row'] = // ...

    5

    6

    // After 😍

    7

    let movie: Tables<'movies'>

### Response types for complex queries#

`supabase-js` always returns a `data` object (for success), and an `error` object (for unsuccessful requests).

These helper types provide the result types from any query, including nested types for database joins.

Given the following schema with a relation between cities and countries, we can get the nested `CountriesWithCities` type:


    1

    create table countries (

    2

      "id" serial primary key,

    3

      "name" text

    4

    );

    5

    6

    create table cities (

    7

      "id" serial primary key,

    8

      "name" text,

    9

      "country_id" int references "countries"

    10

    );


    1

    import { QueryResult, QueryData, QueryError } from '@supabase/supabase-js'

    2

    3

    const countriesWithCitiesQuery = supabase

    4

      .from("countries")

    5

      .select(`

    6

        id,

    7

        name,

    8

        cities (

    9

          id,

    10

          name

    11

        )

    12

      `);

    13

    type CountriesWithCities = QueryData<typeof countriesWithCitiesQuery>;

    14

    15

    const { data, error } = await countriesWithCitiesQuery;

    16

    if (error) throw error;

    17

    const countriesWithCities: CountriesWithCities = data;

* * *

## Fetch data

`select(columns?, options?)`

Perform a SELECT query on the table or view.

When using `count` with `.range()` or `.limit()`, the returned `count` is the total number of rows that match your filters, not the number of rows in the current page. Use this to build pagination UI.

  * By default, Supabase projects return a maximum of 1,000 rows. This setting can be changed in your project's [API settings](/dashboard/project/_/settings/api). It's recommended that you keep it low to limit the payload size of accidental or malicious requests. You can use `range()` queries to paginate through your data.
  * `select()` can be combined with [Filters](/docs/reference/javascript/using-filters)
  * `select()` can be combined with [Modifiers](/docs/reference/javascript/using-modifiers)
  * `apikey` is a reserved keyword if you're using the [Supabase Platform](/docs/guides/platform) and [should be avoided as a column name](https://github.com/supabase/supabase/issues/5465). *


### Parameters

  * columns

Optional

Query

The columns to retrieve, separated by commas. Columns can be renamed when returned with `customName:columnName`

  * options

Optional

object

Named parameters

Details


Getting your dataSelecting specific columnsQuery referenced tablesQuery referenced tables with spaces in their namesQuery referenced tables through a join tableQuery the same referenced table multiple timesFiltering through referenced tablesQuerying referenced table with countQuerying with count optionQuerying JSON dataQuerying referenced table with inner joinSwitching schemas per query


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

Data source

Response

* * *

## Insert data

`insert(values, options)`

Perform an INSERT into the table or view.

By default, inserted rows are not returned. To return it, chain the call with `.select()`.

### Parameters

  * valuesOne of the following options

The values to insert. Pass an object to insert a single row or an array to insert multiple rows.

Details

    * Option 1RejectExcessProperties

    * Option 2Array<RejectExcessProperties>

  * optionsobject

Named parameters

Details


Create a recordCreate a record and return itBulk create


    1

    const { error } = await supabase

    2

      .from('countries')

    3

      .insert({ id: 1, name: 'Mordor' })

Data source

Response

* * *

## Update data

`update(values, options)`

Perform an UPDATE on the table or view.

By default, updated rows are not returned. To return it, chain the call with `.select()` after filters.

  * `update()` should always be combined with [Filters](/docs/reference/javascript/using-filters) to target the item(s) you wish to update.


### Parameters

  * valuesRejectExcessProperties

The values to update with

  * optionsobject

Named parameters

Details


Updating your dataUpdate a record and return itUpdating JSON data


    1

    const { error } = await supabase

    2

      .from('instruments')

    3

      .update({ name: 'piano' })

    4

      .eq('id', 1)

Data source

Response

* * *

## Upsert data

`upsert(values, options)`

Perform an UPSERT on the table or view. Depending on the column(s) passed to `onConflict`, `.upsert()` allows you to perform the equivalent of `.insert()` if a row with the corresponding `onConflict` columns doesn't exist, or if it does exist, perform an alternative action depending on `ignoreDuplicates`.

By default, upserted rows are not returned. To return it, chain the call with `.select()`.

  * Primary keys must be included in `values` to use upsert.


### Parameters

  * valuesOne of the following options

The values to upsert with. Pass an object to upsert a single row or an array to upsert multiple rows.

Details

    * Option 1RejectExcessProperties

    * Option 2Array<RejectExcessProperties>

  * optionsobject

Named parameters

Details


Upsert a single row using a unique keyUpsert with conflict resolution and exact row countingUpsert your dataBulk Upsert your dataUpserting into tables with constraints


    1

    // Upserting a single row, overwriting based on the 'username' unique column

    2

    const { data, error } = await supabase

    3

      .from('users')

    4

      .upsert({ username: 'supabot' }, { onConflict: 'username' })

    5

    6

    // Example response:

    7

    // {

    8

    //   data: [

    9

    //     { id: 4, message: 'bar', username: 'supabot' }

    10

    //   ],

    11

    //   error: null

    12

    // }

* * *

## Delete data

`delete(options)`

Perform a DELETE on the table or view.

By default, deleted rows are not returned. To return it, chain the call with `.select()` after filters.

  * `delete()` should always be combined with [filters](/docs/reference/javascript/using-filters) to target the item(s) you wish to delete.
  * If you use `delete()` with filters and you have [RLS](/docs/learn/auth-deep-dive/auth-row-level-security) enabled, only rows visible through `SELECT` policies are deleted. Note that by default no rows are visible, so you need at least one `SELECT`/`ALL` policy that makes the rows visible.
  * When using `delete().in()`, specify an array of values to target multiple rows with a single query. This is particularly useful for batch deleting entries that share common criteria, such as deleting users by their IDs. Ensure that the array you provide accurately represents all records you intend to delete to avoid unintended data removal.


### Parameters

  * optionsobject

Named parameters

Details


Delete a single recordDelete a record and return itDelete multiple records


    1

    const response = await supabase

    2

      .from('countries')

    3

      .delete()

    4

      .eq('id', 1)

Data source

Response

* * *

## Call a Postgres function

`rpc(fn, args, options)`

Perform a function call.

### Parameters

  * fnFnName

The function name to call

  * argsArgs

The arguments to pass to the function call

  * optionsobject

Named parameters

Details


Example 1Call a Postgres function without argumentsCall a Postgres function with argumentsBulk processingCall a Postgres function with filtersCall a read-only Postgres function


    1

    // For cross-schema functions where type inference fails, use overrideTypes:

    2

    const { data } = await supabase

    3

      .schema('schema_b')

    4

      .rpc('function_a', {})

    5

      .overrideTypes<{ id: string; user_id: string }[]>()

* * *

## Using filters

Filters allow you to only return rows that match certain conditions.

Filters can be used on `select()`, `update()`, `upsert()`, and `delete()` queries.

If a Postgres function returns a table response, you can also apply filters.

Applying FiltersChainingConditional ChainingFilter by values within a JSON columnFilter referenced tables


    1

    const { data, error } = await supabase

    2

      .from('instruments')

    3

      .select('name, section_id')

    4

      .eq('name', 'violin')    // Correct

    5

    6

    const { data, error } = await supabase

    7

      .from('instruments')

    8

      .eq('name', 'violin')    // Incorrect

    9

      .select('name, section_id')

Notes

* * *

## Column is equal to a value

`eq(column, value)`

Match only rows where `column` is equal to `value`.

To check if the value of `column` is NULL, you should use `.is()` instead.

### Parameters

  * column

The column to filter on

  * value

The value to filter with


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .eq('name', 'Leia')

Data source

Response

* * *

## Column is not equal to a value

`neq(column, value)`

Match only rows where `column` is not equal to `value`.

This filter does not include rows where `column` is `NULL`. To match null values, use `.is(column, null)` instead.

### Parameters

  * column

The column to filter on

  * value

The value to filter with


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .neq('name', 'Leia')

Data source

Response

* * *

## Column is greater than a value

`gt(column, value)`

Match only rows where `column` is greater than `value`.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * valueOne of the following options

Details

    * Option 1Row['ColumnName']

    * Option 2unknown


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .gt('id', 2)

Data source

Response

Notes

* * *

## Column is greater than or equal to a value

`gte(column, value)`

Match only rows where `column` is greater than or equal to `value`.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * valueOne of the following options

Details

    * Option 1Row['ColumnName']

    * Option 2unknown


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .gte('id', 2)

Data source

Response

* * *

## Column is less than a value

`lt(column, value)`

Match only rows where `column` is less than `value`.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * valueOne of the following options

Details

    * Option 1Row['ColumnName']

    * Option 2unknown


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .lt('id', 2)

Data source

Response

* * *

## Column is less than or equal to a value

`lte(column, value)`

Match only rows where `column` is less than or equal to `value`.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * valueOne of the following options

Details

    * Option 1Row['ColumnName']

    * Option 2unknown


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .lte('id', 2)

Data source

Response

* * *

## Column matches a pattern

`like(column, pattern)`

Match only rows where `column` matches `pattern` case-sensitively.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * patternstring


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .like('name', '%Lu%')

Data source

Response

* * *

## Column matches a case-insensitive pattern

`ilike(column, pattern)`

Match only rows where `column` matches `pattern` case-insensitively.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * patternstring


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .ilike('name', '%lu%')

Data source

Response

* * *

## Column is a value

`is(column, value)`

Match only rows where `column` IS `value`.

For non-boolean columns, this is only relevant for checking if the value of `column` is NULL by setting `value` to `null`.

For boolean columns, you can also set `value` to `true` or `false` and it will behave the same way as `.eq()`.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * valueOne of the following options

Details

    * Option 1null

    * Option 2boolean


### Return Type

this

Checking for nullness, true or false


    1

    const { data, error } = await supabase

    2

      .from('countries')

    3

      .select()

    4

      .is('name', null)

Data source

Response

Notes

* * *

## Column is in an array

`in(column, values)`

Match only rows where `column` is included in the `values` array.

### Parameters

  * columnColumnName

The column to filter on

  * valuesArray

The values array to filter with


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .in('name', ['Leia', 'Han'])

Data source

Response

* * *

## Column contains every element in a value

`contains(column, value)`

Only relevant for jsonb, array, and range columns. Match only rows where `column` contains every element appearing in `value`.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * valueOne of the following options

Details

    * Option 1string

    * Option 2Record<string, unknown>

    * Option 3Array<Row['ColumnName']>

    * Option 4Array<unknown>


### Return Type

this

On array columnsOn range columnsOn `jsonb` columns


    1

    const { data, error } = await supabase

    2

      .from('issues')

    3

      .select()

    4

      .contains('tags', ['is:open', 'priority:low'])

Data source

Response

* * *

## Contained by value

`containedBy(column, value)`

Only relevant for jsonb, array, and range columns. Match only rows where every element appearing in `column` is contained by `value`.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * valueOne of the following options

Details

    * Option 1string

    * Option 2Record<string, unknown>

    * Option 3Array<Row['ColumnName']>

    * Option 4Array<unknown>


### Return Type

this

On array columnsOn range columnsOn `jsonb` columns


    1

    const { data, error } = await supabase

    2

      .from('classes')

    3

      .select('name')

    4

      .containedBy('days', ['monday', 'tuesday', 'wednesday', 'friday'])

Data source

Response

* * *

## Greater than a range

`rangeGt(column, range)`

Only relevant for range columns. Match only rows where every element in `column` is greater than any element in `range`.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * rangestring


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('reservations')

    3

      .select()

    4

      .rangeGt('during', '[2000-01-02 08:00, 2000-01-02 09:00)')

Data source

Response

Notes

* * *

## Greater than or equal to a range

`rangeGte(column, range)`

Only relevant for range columns. Match only rows where every element in `column` is either contained in `range` or greater than any element in `range`.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * rangestring


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('reservations')

    3

      .select()

    4

      .rangeGte('during', '[2000-01-02 08:30, 2000-01-02 09:30)')

Data source

Response

Notes

* * *

## Less than a range

`rangeLt(column, range)`

Only relevant for range columns. Match only rows where every element in `column` is less than any element in `range`.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * rangestring


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('reservations')

    3

      .select()

    4

      .rangeLt('during', '[2000-01-01 15:00, 2000-01-01 16:00)')

Data source

Response

Notes

* * *

## Less than or equal to a range

`rangeLte(column, range)`

Only relevant for range columns. Match only rows where every element in `column` is either contained in `range` or less than any element in `range`.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * rangestring


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('reservations')

    3

      .select()

    4

      .rangeLte('during', '[2000-01-01 14:00, 2000-01-01 16:00)')

Data source

Response

Notes

* * *

## Mutually exclusive to a range

`rangeAdjacent(column, range)`

Only relevant for range columns. Match only rows where `column` is mutually exclusive to `range` and there can be no element between the two ranges.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * rangestring


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('reservations')

    3

      .select()

    4

      .rangeAdjacent('during', '[2000-01-01 12:00, 2000-01-01 13:00)')

Data source

Response

Notes

* * *

## With a common element

`overlaps(column, value)`

Only relevant for array and range columns. Match only rows where `column` and `value` have an element in common.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * valueOne of the following options

Details

    * Option 1string

    * Option 2Array<Row['ColumnName']>

    * Option 3Array<unknown>


### Return Type

this

On array columnsOn range columns


    1

    const { data, error } = await supabase

    2

      .from('issues')

    3

      .select('title')

    4

      .overlaps('tags', ['is:closed', 'severity:high'])

Data source

Response

* * *

## Match a string

`textSearch(column, query, options?)`

Only relevant for text and tsvector columns. Match only rows where `column` matches the query string in `query`.

  * For more information, see [Postgres full text search](/docs/guides/database/full-text-search).


### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * querystring

  * options

Optional

object

Details


### Return Type

this

Text searchBasic normalizationFull normalizationWebsearch


    1

    const result = await supabase

    2

      .from("texts")

    3

      .select("content")

    4

      .textSearch("content", `'eggs' & 'ham'`, {

    5

        config: "english",

    6

      });

Data source

Response

* * *

## Match an associated value

`match(query)`

Match only rows where each column in `query` keys is equal to its associated value. Shorthand for multiple `.eq()`s.

### Parameters

  * queryOne of the following options

Details

    * Option 1Record<ColumnName, Row['ColumnName']>

    * Option 2Record<string, unknown>


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select('name')

    4

      .match({ id: 2, name: 'Leia' })

Data source

Response

* * *

## Don't match the filter

`not(column, operator, value)`

Match only rows which doesn't satisfy the filter.

Unlike most filters, `opearator` and `value` are used as-is and need to follow [PostgREST syntax](https://postgrest.org/en/stable/api.html#operators). You also need to make sure they are properly sanitized.

not() expects you to use the raw PostgREST syntax for the filter values.


    1

    .not('id', 'in', '(5,6,7)')  // Use `()` for `in` filter

    2

    .not('arraycol', 'cs', '{"a","b"}')  // Use `cs` for `contains()`, `{}` for array values

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * operatorOne of the following options

Details

    * Option 1"is"

    * Option 2FilterOperator

    * Option 3string

  * valueOne of the following options

Details

    * Option 1null

    * Option 2Row['ColumnName']

    * Option 3unknown


With `select()`


    1

    const { data, error } = await supabase

    2

      .from('countries')

    3

      .select()

    4

      .not('name', 'is', null)

Data source

Response

* * *

## Match at least one filter

`or(filters, options)`

Match only rows which satisfy at least one of the filters.

Unlike most filters, `filters` is used as-is and needs to follow [PostgREST syntax](https://postgrest.org/en/stable/api.html#operators). You also need to make sure it's properly sanitized.

It's currently not possible to do an `.or()` filter across multiple tables.

or() expects you to use the raw PostgREST syntax for the filter names and values.


    1

    .or('id.in.(5,6,7), arraycol.cs.{"a","b"}')  // Use `()` for `in` filter, `{}` for array values and `cs` for `contains()`.

    2

    .or('id.in.(5,6,7), arraycol.cd.{"a","b"}')  // Use `cd` for `containedBy()`

### Parameters

  * filtersstring

The filters to use, following PostgREST syntax

  * optionsobject

Named parameters

Details


### Return Type

this

With `select()`Use `or` with `and`Use `or` on referenced tables


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select('name')

    4

      .or('id.eq.2,name.eq.Han')

Data source

Response

* * *

## Match the filter

`filter(column, operator, value)`

Match only rows which satisfy the filter. This is an escape hatch - you should use the specific filter methods wherever possible.

Unlike most filters, `opearator` and `value` are used as-is and need to follow [PostgREST syntax](https://postgrest.org/en/stable/api.html#operators). You also need to make sure they are properly sanitized.

filter() expects you to use the raw PostgREST syntax for the filter values.


    1

    .filter('id', 'in', '(5,6,7)')  // Use `()` for `in` filter

    2

    .filter('arraycol', 'cs', '{"a","b"}')  // Use `cs` for `contains()`, `{}` for array values

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * operatorOne of the following options

Details

    * Option 1FilterOperator

    * Option 2"not.match"

    * Option 3"not.is"

    * Option 4"not.eq"

    * Option 5"not.neq"

    * Option 6"not.gt"

    * Option 7"not.gte"

    * Option 8"not.lt"

    * Option 9"not.lte"

    * Option 10"not.like"

    * Option 11"not.ilike"

    * Option 12"not.isdistinct"

    * Option 13"not.in"

    * Option 14"not.cs"

    * Option 15"not.cd"

    * Option 16"not.sl"

    * Option 17"not.sr"

    * Option 18"not.nxl"

    * Option 19"not.nxr"

    * Option 20"not.adj"

    * Option 21"not.ov"

    * Option 22"not.fts"

    * Option 23"not.plfts"

    * Option 24"not.phfts"

    * Option 25"not.wfts"

    * Option 26"not.imatch"

    * Option 27string

  * valueunknown


### Return Type

this

With `select()`On a referenced table


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .filter('name', 'in', '("Han","Yoda")')

Data source

Response

* * *

## Using modifiers

Filters work on the row level—they allow you to return rows that only match certain conditions without changing the shape of the rows. Modifiers are everything that don't fit that definition—allowing you to change the format of the response (e.g., returning a CSV string).

Modifiers must be specified after filters. Some modifiers only apply for queries that return rows (e.g., `select()` or `rpc()` on a function that returns a table response).

* * *

## Return data after inserting

`select(columns?)`

Perform a SELECT on the query result.

By default, `.insert()`, `.update()`, `.upsert()`, and `.delete()` do not return modified rows. By calling this method, modified rows are returned in `data`.

### Parameters

  * columns

Optional

Query

The columns to retrieve, separated by commas


With `upsert()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .upsert({ id: 1, name: 'Han Solo' })

    4

      .select()

Data source

Response

* * *

## Order the results

`order(column, options?)`

Order the query result by `column`.

You can call this method multiple times to order by multiple columns.

You can order referenced tables, but it only affects the ordering of the parent table if you use `!inner` in the query.

### Parameters

  * columnOne of the following options

Details

    * Option 1ColumnName

    * Option 2string

  * options

Optional

object

Details


### Return Type

this

With `select()`On a referenced tableOrder parent table by a referenced table


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select('id, name')

    4

      .order('id', { ascending: false })

Data source

Response

* * *

## Limit the number of rows returned

`limit(count, options)`

Limit the query result by `count`.

### Parameters

  * countnumber

The maximum number of rows to return

  * optionsobject

Named parameters

Details


### Return Type

this

With `select()`On a referenced table


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select('name')

    4

      .limit(1)

Data source

Response

* * *

## Limit the query to a range

`range(from, to, options)`

Limit the query result by starting at an offset `from` and ending at the offset `to`. Only records within this range are returned. This respects the query order and if there is no order clause the range could behave unexpectedly. The `from` and `to` values are 0-based and inclusive: `range(1, 3)` will include the second, third and fourth rows of the query.

### Parameters

  * fromnumber

The starting index from which to limit the result

  * tonumber

The last index to which to limit the result

  * optionsobject

Named parameters

Details


### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select('name')

    4

      .range(0, 1)

Data source

Response

* * *

## Set an abort signal

`abortSignal(signal)`

Set the AbortSignal for the fetch request.

You can use this to set a timeout for the request.

### Parameters

  * signalAbortSignal

The AbortSignal to use for the fetch request


### Return Type

this

Aborting requests in-flightSet a timeout


    1

    const ac = new AbortController()

    2

    3

    const { data, error } = await supabase

    4

      .from('very_big_table')

    5

      .select()

    6

      .abortSignal(ac.signal)

    7

    8

    // Abort the request after 100 ms

    9

    setTimeout(() => ac.abort(), 100)

Response

Notes

* * *

## Retrieve one row of data

`single()`

Return `data` as a single object instead of an array of objects.

Query result must be one row (e.g. using `.limit(1)`), otherwise this returns an error.

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select('name')

    4

      .limit(1)

    5

      .single()

Data source

Response

* * *

## Retrieve zero or one row of data

`maybeSingle()`

Return `data` as a single object instead of an array of objects.

Query result must be zero or one row (e.g. using `.limit(1)`), otherwise this returns an error.

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .eq('name', 'Katniss')

    5

      .maybeSingle()

Data source

Response

* * *

## Retrieve as a CSV

`csv()`

Return `data` as a string in CSV format.

Return data as CSV


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .csv()

Data source

Response

Notes

* * *

## Strip null values

`stripNulls()`

Strip null values from the response data. Properties with `null` values will be omitted from the returned JSON objects.

Requires PostgREST 11.2.0+.

[https://docs.postgrest.org/en/stable/references/api/resource\\_representation.html#stripped-nulls](https://docs.postgrest.org/en/stable/references/api/resource%5C_representation.html#stripped-nulls)

### Return Type

this

With `select()`


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .stripNulls()

Data source

Response

* * *

## Override type of successful response

`returns()`

Override the type of the returned `data`.

  * Deprecated: use overrideTypes method instead


Override type of successful responseOverride type of object response


    1

    const { data } = await supabase

    2

      .from('countries')

    3

      .select()

    4

      .returns<Array<MyType>>()

Response

* * *

## Partially override or replace type of successful response

`overrideTypes()`

Override the type of the returned `data` field in the response.

Example 1Complete Override type of successful responseComplete Override type of object responsePartial Override type of successful responsePartial Override type of object responseMerge vs replace existing types


    1

    // Merge with existing types (default behavior)

    2

    const query = supabase

    3

      .from('users')

    4

      .select()

    5

      .overrideTypes<{ custom_field: string }>()

    6

    7

    // Replace existing types completely

    8

    const replaceQuery = supabase

    9

      .from('users')

    10

      .select()

    11

      .overrideTypes<{ id: number; name: string }, { merge: false }>()

* * *

## Using explain

`explain(options)`

Return `data` as the EXPLAIN plan for the query.

You need to enable the [db_plan_enabled](https://supabase.com/docs/guides/database/debugging-performance#enabling-explain) setting before using this method.

### Parameters

  * optionsobject

Named parameters

Details


### Return Type

One of the following options

Details

  * Option 1PostgrestBuilder

  * Option 2PostgrestBuilder


Get the execution planGet the execution plan with analyze and verbose


    1

    const { data, error } = await supabase

    2

      .from('characters')

    3

      .select()

    4

      .explain()

Data source

Response

Notes

* * *

## Overview

  * The auth methods can be accessed via the `supabase.auth` namespace.

  * By default, the supabase client sets `persistSession` to true and attempts to store the session in local storage. When using the supabase client in an environment that doesn't support local storage, you might notice the following warning message being logged:

> No storage option exists to persist the session, which may result in unexpected behavior when using auth. If you want to set `persistSession` to true, please provide a storage option or you may set `persistSession` to false to disable this warning.

This warning message can be safely ignored if you're not using auth on the server-side. If you are using auth and you want to set `persistSession` to true, you will need to provide a custom storage implementation that follows [this interface](https://github.com/supabase/supabase-js/blob/master/packages/core/auth-js/src/lib/types.ts#L1053).

  * Any email links and one-time passwords (OTPs) sent have a default expiry of 24 hours. We have the following [rate limits](/docs/guides/platform/going-into-prod#auth-rate-limits) in place to guard against brute force attacks.

  * The expiry of an access token can be set in the "JWT expiry limit" field in [your project's auth settings](/dashboard/project/_/auth/providers). A refresh token never expires and can only be used once.


Create auth clientCreate auth client (server-side)


    1

    import { createClient } from '@supabase/supabase-js'

    2

    3

    const supabase = createClient(supabase_url, publishable_key)

* * *

## Create a new user

`signUp(credentials)`

Creates a new user.

Be aware that if a user account exists in the system you may get back an error message that attempts to hide this information from the user. This method has support for PKCE via email signups. The PKCE flow cannot be used when autoconfirm is enabled.

  * By default, the user needs to verify their email address before logging in. To turn this off, disable **Confirm email** in [your project](/dashboard/project/_/auth/providers).
  * **Confirm email** determines if users need to confirm their email address after signing up.
    * If **Confirm email** is enabled, a `user` is returned but `session` is null.
    * If **Confirm email** is disabled, both a `user` and a `session` are returned.
  * When the user confirms their email address, they are redirected to the [`SITE_URL`](/docs/guides/auth/redirect-urls#use-wildcards-in-redirect-urls) by default. You can modify your `SITE_URL` or add additional redirect URLs in [your project](/dashboard/project/_/auth/url-configuration).
  * If signUp() is called for an existing confirmed user:
    * When both **Confirm email** and **Confirm phone** (even when phone provider is disabled) are enabled in [your project](/dashboard/project/_/auth/providers), an obfuscated/fake user object is returned.
    * When either **Confirm email** or **Confirm phone** (even when phone provider is disabled) is disabled, the error message, `User already registered` is returned.
  * To fetch the currently logged-in user, refer to [`getUser()`](/docs/reference/javascript/auth-getuser).


### Parameters

  * credentialsSignUpWithPasswordCredentials


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Sign up with an email and passwordSign up with a phone number and password (SMS)Sign up with a phone number and password (whatsapp)Sign up with additional user metadataSign up with a redirect URL


    1

    const { data, error } = await supabase.auth.signUp({

    2

      email: 'example@email.com',

    3

      password: 'example-password',

    4

    })

Response

* * *

## Listen to auth events

`onAuthStateChange(callback)`

Receive a notification every time an auth event happens. Safe to use without an async function as callback.

  * Subscribes to important events occurring on the user's session.
  * Use on the frontend/client. It is less useful on the server.
  * Events are emitted across tabs to keep your application's UI up-to-date. Some events can fire very frequently, based on the number of tabs open. Use a quick and efficient callback function, and defer or debounce as many operations as you can to be performed outside of the callback.
  * **Important:** A callback can be an `async` function and it runs synchronously during the processing of the changes causing the event. You can easily create a dead-lock by using `await` on a call to another method of the Supabase library.
    * Avoid using `async` functions as callbacks.
    * Limit the number of `await` calls in `async` callbacks.
    * Do not use other Supabase functions in the callback function. If you must, dispatch the functions once the callback has finished executing. Use this as a quick way to achieve this:

          1

          supabase.auth.onAuthStateChange((event, session) => {

          2

            setTimeout(async () => {

          3

              // await on other Supabase function here

          4

              // this runs right after the callback has finished

          5

            }, 0)

          6

          })

  * Emitted events:
    * `INITIAL_SESSION`
      * Emitted right after the Supabase client is constructed and the initial session from storage is loaded.
    * `SIGNED_IN`
      * Emitted each time a user session is confirmed or re-established, including on user sign in and when refocusing a tab.
      * Avoid making assumptions as to when this event is fired, this may occur even when the user is already signed in. Instead, check the user object attached to the event to see if a new user has signed in and update your application's UI.
      * This event can fire very frequently depending on the number of tabs open in your application.
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
        * Avoid frequent calls to `supabase.auth.getSession()` for the same purpose.
      * There is a background process that keeps track of when the session should be refreshed so you will always receive valid tokens by listening to this event.
      * The frequency of this event is related to the JWT expiry limit configured on your project.
    * `USER_UPDATED`
      * Emitted each time the `supabase.auth.updateUser()` method finishes successfully. Listen to it to update your application's UI based on new profile information.
    * `PASSWORD_RECOVERY`
      * Emitted instead of the `SIGNED_IN` event when the user lands on a page that includes a password recovery link in the URL.
      * Use it to show a UI to the user where they can [reset their password](/docs/guides/auth/passwords#resetting-a-users-password-forgot-password).


### Parameters

  * callbackfunction

A callback function to be invoked when an auth event happens.

Details


### Return Type

object

Details

Listen to auth changesListen to sign outStore OAuth provider tokens on sign inUse React Context for the User's sessionListen to password recovery eventsListen to sign inListen to token refreshListen to user updates


    1

    const { data } = supabase.auth.onAuthStateChange((event, session) => {

    2

      console.log(event, session)

    3

    4

      if (event === 'INITIAL_SESSION') {

    5

        // handle initial session

    6

      } else if (event === 'SIGNED_IN') {

    7

        // handle sign in event

    8

      } else if (event === 'SIGNED_OUT') {

    9

        // handle sign out event

    10

      } else if (event === 'PASSWORD_RECOVERY') {

    11

        // handle password recovery event

    12

      } else if (event === 'TOKEN_REFRESHED') {

    13

        // handle token refreshed event

    14

      } else if (event === 'USER_UPDATED') {

    15

        // handle user updated event

    16

      }

    17

    })

    18

    19

    // call unsubscribe to remove the callback

    20

    data.subscription.unsubscribe()

* * *

## Create an anonymous user

`signInAnonymously(credentials?)`

Creates a new anonymous user.

  * Returns an anonymous user
  * It is recommended to set up captcha for anonymous sign-ins to prevent abuse. You can pass in the captcha token in the `options` param.


### Parameters

  * credentials

Optional

SignInAnonymouslyCredentials

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Create an anonymous userCreate an anonymous user with custom user metadata


    1

    const { data, error } = await supabase.auth.signInAnonymously({

    2

      options: {

    3

        captchaToken

    4

      }

    5

    });

Response

* * *

## Sign in a user

`signInWithPassword(credentials)`

Log in an existing user with an email and password or phone and password.

Be aware that you may get back an error message that will not distinguish between the cases where the account does not exist or that the email/phone and password combination is wrong or that the account can only be accessed via social login.

  * Requires either an email and password or a phone number and password.


### Parameters

  * credentialsSignInWithPasswordCredentials


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Sign in with email and passwordSign in with phone and password


    1

    const { data, error } = await supabase.auth.signInWithPassword({

    2

      email: 'example@email.com',

    3

      password: 'example-password',

    4

    })

Response

* * *

## Sign in with ID token (native sign-in)

`signInWithIdToken(credentials)`

Allows signing in with an OIDC ID token. The authentication provider used should be enabled and configured.

  * Use an ID token to sign in.
  * Especially useful when implementing sign in using native platform dialogs in mobile or desktop apps using Sign in with Apple or Sign in with Google on iOS and Android.
  * You can also use Google's [One Tap](https://developers.google.com/identity/gsi/web/guides/display-google-one-tap) and [Automatic sign-in](https://developers.google.com/identity/gsi/web/guides/automatic-sign-in-sign-out) via this API.


### Parameters

  * credentialsSignInWithIdTokenCredentials

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Sign In using ID Token


    1

    const { data, error } = await supabase.auth.signInWithIdToken({

    2

      provider: 'google',

    3

      token: 'your-id-token'

    4

    })

Response

* * *

## Sign in a user through OTP

`signInWithOtp(credentials)`

Log in a user using magiclink or a one-time password (OTP).

If the `{{ .ConfirmationURL }}` variable is specified in the email template, a magiclink will be sent. If the `{{ .Token }}` variable is specified in the email template, an OTP will be sent. If you're using phone sign-ins, only an OTP will be sent. You won't be able to send a magiclink for phone sign-ins.

Be aware that you may get back an error message that will not distinguish between the cases where the account does not exist or, that the account can only be accessed via social login.

Do note that you will need to configure a Whatsapp sender on Twilio if you are using phone sign in with the 'whatsapp' channel. The whatsapp channel is not supported on other providers at this time. This method supports PKCE when an email is passed.

  * Requires either an email or phone number.
  * This method is used for passwordless sign-ins where a OTP is sent to the user's email or phone number.
  * If the user doesn't exist, `signInWithOtp()` will signup the user instead. To restrict this behavior, you can set `shouldCreateUser` in `SignInWithPasswordlessCredentials.options` to `false`.
  * If you're using an email, you can configure whether you want the user to receive a magiclink or a OTP.
  * If you're using phone, you can configure whether you want the user to receive a OTP.
  * The magic link's destination URL is determined by the [`SITE_URL`](/docs/guides/auth/redirect-urls#use-wildcards-in-redirect-urls).
  * See [redirect URLs and wildcards](/docs/guides/auth/redirect-urls#use-wildcards-in-redirect-urls) to add additional redirect URLs to your project.
  * Magic links and OTPs share the same implementation. To send users a one-time code instead of a magic link, [modify the magic link email template](/dashboard/project/_/auth/templates) to include `{{ .Token }}` instead of `{{ .ConfirmationURL }}`.
  * See our [Twilio Phone Auth Guide](/docs/guides/auth/phone-login?showSMSProvider=Twilio) for details about configuring WhatsApp sign in.


### Parameters

  * credentialsOne of the following options

Details

    * Option 1object

Details

    * Option 2object

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Sign in with emailSign in with SMS OTPSign in with WhatsApp OTP


    1

    const { data, error } = await supabase.auth.signInWithOtp({

    2

      email: 'example@email.com',

    3

      options: {

    4

        emailRedirectTo: 'https://example.com/welcome'

    5

      }

    6

    })

Response

Notes

* * *

## Sign in a user through OAuth

`signInWithOAuth(credentials)`

Log in an existing user via a third-party provider. This method supports the PKCE flow.

  * This method is used for signing in using [Social Login (OAuth) providers](/docs/guides/auth#configure-third-party-providers).
  * It works by redirecting your application to the provider's authorization screen, before bringing back the user to your app.


### Parameters

  * credentialsSignInWithOAuthCredentials

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Sign in using a third-party providerSign in using a third-party provider with redirectSign in with scopes and access provider tokens


    1

    const { data, error } = await supabase.auth.signInWithOAuth({

    2

      provider: 'github'

    3

    })

Response

* * *

## Sign in a user through SSO

`signInWithSSO(params)`

Attempts a single-sign on using an enterprise Identity Provider. A successful SSO attempt will redirect the current page to the identity provider authorization page. The redirect URL is implementation and SSO protocol specific.

You can use it by providing a SSO domain. Typically you can extract this domain by asking users for their email address. If this domain is registered on the Auth instance the redirect will use that organization's currently active SSO Identity Provider for the login.

If you have built an organization-specific login page, you can use the organization's SSO Identity Provider UUID directly instead.

  * Before you can call this method you need to [establish a connection](/docs/guides/auth/sso/auth-sso-saml#managing-saml-20-connections) to an identity provider. Use the [CLI commands](/docs/reference/cli/supabase-sso) to do this.
  * If you've associated an email domain to the identity provider, you can use the `domain` property to start a sign-in flow.
  * In case you need to use a different way to start the authentication flow with an identity provider, you can use the `providerId` property. For example:
    * Mapping specific user email addresses with an identity provider.
    * Using different hints to identity the identity provider to be used by the user, like a company-specific page, IP address or other tracking information.


### Parameters

  * paramsOne of the following options

Details

    * Option 1object

Details

    * Option 2object

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Sign in with email domainSign in with provider UUID


    1

    // You can extract the user's email domain and use it to trigger the

    2

      // authentication flow with the correct identity provider.

    3

    4

      const { data, error } = await supabase.auth.signInWithSSO({

    5

        domain: 'company.com'

    6

      })

    7

    8

      if (data?.url) {

    9

        // redirect the user to the identity provider's authentication flow

    10

        window.location.href = data.url

    11

      }

* * *

## Sign in a user through Web3 (Solana, Ethereum)

`signInWithWeb3(credentials)`

Signs in a user by verifying a message signed by the user's private key. Supports Ethereum (via Sign-In-With-Ethereum) & Solana (Sign-In-With-Solana) standards, both of which derive from the EIP-4361 standard With slight variation on Solana's side.

  * Uses a Web3 (Ethereum, Solana) wallet to sign a user in.
  * Read up on the [potential for abuse](/docs/guides/auth/auth-web3#potential-for-abuse) before using it.


### Parameters

  * credentialsOne of the following options

Details

    * Option 1One of the following options

Details

      * Option 1object

Details

      * Option 2object

Details

    * Option 2One of the following options

Details

      * Option 1object

Details

      * Option 2object

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Sign in with Solana or Ethereum (Window API)Sign in with Ethereum (Message and Signature)Sign in with Solana (Brave)Sign in with Solana (Wallet Adapter)


    1

    // uses window.ethereum for the wallet

    2

      const { data, error } = await supabase.auth.signInWithWeb3({

    3

        chain: 'ethereum',

    4

        statement: 'I accept the Terms of Service at https://example.com/tos'

    5

      })

    6

    7

      // uses window.solana for the wallet

    8

      const { data, error } = await supabase.auth.signInWithWeb3({

    9

        chain: 'solana',

    10

        statement: 'I accept the Terms of Service at https://example.com/tos'

    11

      })

* * *

## Sign in with a passkey

`signInWithPasskey(credentials?)`

Sign in with a passkey. Handles the full WebAuthn ceremony:

  1. Fetches authentication challenge from server
  2. Prompts user via navigator.credentials.get()
  3. Verifies credential with server and creates session


Requires `auth.experimental.passkey: true`.

### Parameters

  * credentials

Optional

SignInWithPasskeyCredentials

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Register a passkey

`registerPasskey(credentials?)`

Register a passkey for the current authenticated user. Handles the full WebAuthn ceremony:

  1. Fetches registration challenge from server
  2. Prompts user via navigator.credentials.create()
  3. Verifies credential with server


Requires an active session. Requires `auth.experimental.passkey: true`.

### Parameters

  * credentials

Optional

RegisterPasskeyCredentials

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Get user claims from verified JWT

`getClaims(jwt?, options)`

Extracts the JWT claims present in the access token by first verifying the JWT against the server's JSON Web Key Set endpoint `/.well-known/jwks.json` which is often cached, resulting in significantly faster responses. Prefer this method over #getUser which always sends a request to the Auth server for each JWT.

If the project is not using an asymmetric JWT signing key (like ECC or RSA) it always sends a request to the Auth server (similar to #getUser) to verify the JWT.

  * Parses the user's [access token](/docs/guides/auth/sessions#access-token-jwt-claims) as a [JSON Web Token (JWT)](/docs/guides/auth/jwts) and returns its components if valid and not expired.
  * If your project is using asymmetric JWT signing keys, then the verification is done locally usually without a network request using the [WebCrypto API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API).
  * A network request is sent to your project's JWT signing key discovery endpoint `https://project-id.supabase.co/auth/v1/.well-known/jwks.json`, which is cached locally. If your environment is ephemeral, such as a Lambda function that is destroyed after every request, a network request will be sent for each new invocation. Supabase provides a network-edge cache providing fast responses for these situations.
  * If the user's access token is about to expire when calling this function, the user's session will first be refreshed before validating the JWT.
  * If your project is using a symmetric secret to sign the JWT, it always sends a request similar to `getUser()` to validate the JWT at the server before returning the decoded token. This is also used if the WebCrypto API is not available in the environment. Make sure you polyfill it in such situations.
  * The returned claims can be customized per project using the [Custom Access Token Hook](/docs/guides/auth/auth-hooks/custom-access-token-hook).


### Parameters

  * jwt

Optional

string

An optional specific JWT you wish to verify, not the one you can obtain from #getSession.

  * optionsobject

Various additional options that allow you to customize the behavior of this method.

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details

  * Option 3object

Details


Get JWT claims, header and signature


    1

    const { data, error } = await supabase.auth.getClaims()

Response

* * *

## Sign out a user

`signOut(options)`

Inside a browser context, `signOut()` will remove the logged in user from the browser session and log them out - removing all items from localstorage and then trigger a `"SIGNED_OUT"` event.

For server-side management, you can revoke all refresh tokens for a user by passing a user's JWT through to `auth.api.signOut(JWT: string)`. There is no way to revoke a user's access token jwt until it expires. It is recommended to set a shorter expiry on the jwt for this reason.

If using `others` scope, no `SIGNED_OUT` event is fired!

**Warning:** the default `scope` is `'global'`. This signs the user out of **every device they are currently signed in on** , not just the current tab/session. If you only want to sign the user out of the current session (the behavior most other auth libraries default to), pass `{ scope: 'local' }` explicitly.

  * In order to use the `signOut()` method, the user needs to be signed in first.
  * By default, `signOut()` uses the **global** scope, which signs out the user on every device they are signed in on (not just the current one). Pass `{ scope: 'local' }` to only sign out the current session. This is usually what apps want on a "Sign out" button, especially when users sign in from multiple devices and do not expect signing out of one to terminate the others.
  * Since Supabase Auth uses JWTs for authentication, the access token JWT will be valid until it's expired. When the user signs out, Supabase revokes the refresh token and deletes the JWT from the client-side. This does not revoke the JWT and it will still be valid until it expires.


### Parameters

  * optionsSignOut

Details


### Return Type

Promise<object>

Details

Sign out of every device (global – default)Sign out only the current session (recommended for most apps)Sign out of all other sessions, keep the current one


    1

    const { error } = await supabase.auth.signOut()

* * *

## Send a password reset request

`resetPasswordForEmail(email, options)`

Sends a password reset request to an email address. This method supports the PKCE flow.

  * The password reset flow consist of 2 broad steps: (i) Allow the user to login via the password reset link; (ii) Update the user's password.
  * The `resetPasswordForEmail()` only sends a password reset link to the user's email. To update the user's password, see [`updateUser()`](/docs/reference/javascript/auth-updateuser).
  * A `PASSWORD_RECOVERY` event will be emitted when the password recovery link is clicked. You can use [`onAuthStateChange()`](/docs/reference/javascript/auth-onauthstatechange) to listen and invoke a callback function on these events.
  * When the user clicks the reset link in the email they are redirected back to your application. You can configure the URL that the user is redirected to with the `redirectTo` parameter. See [redirect URLs and wildcards](/docs/guides/auth/redirect-urls#use-wildcards-in-redirect-urls) to add additional redirect URLs to your project.
  * After the user has been redirected successfully, prompt them for a new password and call `updateUser()`:




    1

    const { data, error } = await supabase.auth.updateUser({

    2

      password: new_password

    3

    })

### Parameters

  * emailstring

The email address of the user.

  * optionsobject

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Reset passwordReset password (React)


    1

    const { data, error } = await supabase.auth.resetPasswordForEmail(email, {

    2

      redirectTo: 'https://example.com/update-password',

    3

    })

Response

* * *

## Verify and log in through OTP

`verifyOtp(params)`

Log in a user given a User supplied OTP or TokenHash received through mobile or email.

  * The `verifyOtp` method takes in different verification types.
  * If a phone number is used, the type can either be:
    1. `sms` – Used when verifying a one-time password (OTP) sent via SMS during sign-up or sign-in.
    2. `phone_change` – Used when verifying an OTP sent to a new phone number during a phone number update process.
  * If an email address is used, the type can be one of the following (note: `signup` and `magiclink` types are deprecated):
    1. `email` – Used when verifying an OTP sent to the user's email during sign-up or sign-in.
    2. `recovery` – Used when verifying an OTP sent for account recovery, typically after a password reset request.
    3. `invite` – Used when verifying an OTP sent as part of an invitation to join a project or organization.
    4. `email_change` – Used when verifying an OTP sent to a new email address during an email update process.
  * The verification type used should be determined based on the corresponding auth method called before `verifyOtp` to sign up / sign-in a user.
  * The `TokenHash` is contained in the [email templates](/docs/guides/auth/auth-email-templates) and can be used to sign in. You may wish to use the hash for the PKCE flow for Server Side Auth. Read [the Password-based Auth guide](/docs/guides/auth/passwords) for more details.


### Parameters

  * paramsOne of the following options

Details

    * Option 1VerifyMobileOtpParams

Details

    * Option 2VerifyEmailOtpParams

Details

    * Option 3VerifyTokenHashParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Verify Signup One-Time Password (OTP)Verify SMS One-Time Password (OTP)Verify Email Auth (Token Hash)


    1

    const { data, error } = await supabase.auth.verifyOtp({ email, token, type: 'email'})

Response

* * *

## Retrieve a session

`getSession()`

Returns the session, refreshing it if necessary.

The session returned can be null if the session is not detected which can happen in the event a user is not signed-in or has logged out.

**IMPORTANT:** This method loads values directly from the storage attached to the client. If that storage is based on request cookies for example, the values in it may not be authentic and therefore it's strongly advised against using this method and its results in such circumstances. A warning will be emitted if this is detected. Use #getUser() instead.

  * Since the introduction of [asymmetric JWT signing keys](/docs/guides/auth/signing-keys), this method is considered low-level and we encourage you to use `getClaims()` or `getUser()` instead.
  * Retrieves the current [user session](/docs/guides/auth/sessions) from the storage medium (local storage, cookies).
  * The session contains an access token (signed JWT), a refresh token and the user object.
  * If the session's access token is expired or is about to expire, this method will use the refresh token to refresh the session.
  * When using in a browser, or you've called `startAutoRefresh()` in your environment (React Native, etc.) this function always returns a valid access token without refreshing the session itself, as this is done in the background. This function returns very fast.
  * **IMPORTANT SECURITY NOTICE:** If using an insecure storage medium, such as cookies or request headers, the user object returned by this function **must not be trusted**. Always verify the JWT using `getClaims()` or your own JWT verification library to securely establish the user's identity and access. You can also use `getUser()` to fetch the user object directly from the Auth server for this purpose.
  * When using in a browser, this function is synchronized across all tabs using the [LockManager](https://developer.mozilla.org/en-US/docs/Web/API/LockManager) API. In other environments make sure you've defined a proper `lock` property, if necessary, to make sure there are no race conditions while the session is being refreshed.


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details

  * Option 3object

Details


Get the session data


    1

    const { data, error } = await supabase.auth.getSession()

Response

* * *

## Retrieve a new session

`refreshSession(currentSession?)`

Returns a new session, regardless of expiry status. Takes in an optional current session. If not passed in, then refreshSession() will attempt to retrieve it from getSession(). If the current session's refresh token is invalid, an error will be thrown.

  * This method will refresh and return a new session whether the current one is expired or not.


### Parameters

  * currentSession

Optional

object

The current session. If passed in, it must contain a refresh token.

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Refresh session using the current sessionRefresh session using a refresh token


    1

    const { data, error } = await supabase.auth.refreshSession()

    2

    const { session, user } = data

Response

* * *

## Retrieve a user

`getUser(jwt?)`

Gets the current user details if there is an existing session. This method performs a network request to the Supabase Auth server, so the returned value is authentic and can be used to base authorization rules on.

  * This method fetches the user object from the database instead of local session.
  * This method is useful for checking if the user is authorized because it validates the user's access token JWT on the server.
  * Should always be used when checking for user authorization on the server. On the client, you can instead use `getSession().session.user` for faster results. `getSession` is insecure on the server.


### Parameters

  * jwt

Optional

string

Takes in an optional access token JWT. If no JWT is provided, the JWT from the current session is used.


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Get the logged in user with the current existing sessionGet the logged in user with a custom access token jwt


    1

    const { data: { user } } = await supabase.auth.getUser()

Response

* * *

## Update a user

`updateUser(attributes, options)`

Updates user data for a logged in user.

  * In order to use the `updateUser()` method, the user needs to be signed in first.
  * By default, email updates sends a confirmation link to both the user's current and new email. To only send a confirmation link to the user's new email, disable **Secure email change** in your project's [email auth provider settings](/dashboard/project/_/auth/providers).


### Parameters

  * attributesUserAttributes

Details

  * optionsobject

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Update the email for an authenticated userUpdate the phone number for an authenticated userUpdate the password for an authenticated userUpdate the user's metadataUpdate the user's password with a nonce


    1

    const { data, error } = await supabase.auth.updateUser({

    2

      email: 'new@email.com'

    3

    })

Response

Notes

* * *

## Retrieve identities linked to a user

`getUserIdentities()`

Gets all the identities linked to a user.

  * The user needs to be signed in to call `getUserIdentities()`.


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Returns a list of identities linked to the user


    1

    const { data, error } = await supabase.auth.getUserIdentities()

Response

* * *

## Link an identity to a user

`linkIdentity(credentials)`

Links an oauth identity to an existing user. This method supports the PKCE flow.

  * The **Enable Manual Linking** option must be enabled from your [project's authentication settings](/dashboard/project/_/auth/providers).
  * The user needs to be signed in to call `linkIdentity()`.
  * If the candidate identity is already linked to the existing user or another user, `linkIdentity()` will fail.
  * If `linkIdentity` is run in the browser, the user is automatically redirected to the returned URL. On the server, you should handle the redirect.


### Parameters

  * credentialsOne of the following options

Details

    * Option 1SignInWithOAuthCredentials

Details

    * Option 2SignInWithIdTokenCredentials

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Link an identity to a user


    1

    const { data, error } = await supabase.auth.linkIdentity({

    2

      provider: 'github'

    3

    })

Response

* * *

## Unlink an identity from a user

`unlinkIdentity(identity)`

Unlinks an identity from a user by deleting it. The user will no longer be able to sign in with that identity once it's unlinked.

  * The **Enable Manual Linking** option must be enabled from your [project's authentication settings](/dashboard/project/_/auth/providers).
  * The user needs to be signed in to call `unlinkIdentity()`.
  * The user must have at least 2 identities in order to unlink an identity.
  * The identity to be unlinked must belong to the user.


### Parameters

  * identityUserIdentity

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Unlink an identity


    1

    // retrieve all identities linked to a user

    2

    const identities = await supabase.auth.getUserIdentities()

    3

    4

    // find the google identity

    5

    const googleIdentity = identities.find(

    6

      identity => identity.provider === 'google'

    7

    )

    8

    9

    // unlink the google identity

    10

    const { error } = await supabase.auth.unlinkIdentity(googleIdentity)

* * *

## Send a password reauthentication nonce

`reauthenticate()`

Sends a reauthentication OTP to the user's email or phone number. Requires the user to be signed-in.

  * This method is used together with `updateUser()` when a user's password needs to be updated.
  * If you require your user to reauthenticate before updating their password, you need to enable the **Secure password change** option in your [project's email provider settings](/dashboard/project/_/auth/providers).
  * A user is only require to reauthenticate before updating their password if **Secure password change** is enabled and the user **hasn't recently signed in**. A user is deemed recently signed in if the session was created in the last 24 hours.
  * This method will send a nonce to the user's email. If the user doesn't have a confirmed email address, the method will send the nonce to the user's confirmed phone number instead.
  * After receiving the OTP, include it as the `nonce` in your `updateUser()` call to finalize the password change.


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Send reauthentication nonce


    1

    const { error } = await supabase.auth.reauthenticate()

Notes

* * *

## Resend an OTP

`resend(credentials)`

Resends an existing signup confirmation email, email change email, SMS OTP or phone change OTP.

  * Resends a signup confirmation, email change or phone change email to the user.
  * Passwordless sign-ins can be resent by calling the `signInWithOtp()` method again.
  * Password recovery emails can be resent by calling the `resetPasswordForEmail()` method again.
  * This method will only resend an email or phone OTP to the user if there was an initial signup, email change or phone change request being made(note: For existing users signing in with OTP, you should use `signInWithOtp()` again to resend the OTP).
  * You can specify a redirect url when you resend an email link using the `emailRedirectTo` option.


### Parameters

  * credentialsOne of the following options

Details

    * Option 1object

Details

    * Option 2object

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Resend an email signup confirmationResend a phone signup confirmationResend email change emailResend phone change OTP


    1

    const { error } = await supabase.auth.resend({

    2

      type: 'signup',

    3

      email: 'email@example.com',

    4

      options: {

    5

        emailRedirectTo: 'https://example.com/welcome'

    6

      }

    7

    })

Notes

* * *

## Set the session data

`setSession(currentSession)`

Sets the session data from the current session. If the current session is expired, setSession will take care of refreshing it to obtain a new session. If the refresh token or access token in the current session is invalid, an error will be thrown.

  * This method sets the session using an `access_token` and `refresh_token`.
  * If successful, a `SIGNED_IN` event is emitted.


### Parameters

  * currentSessionobject

The current session that minimally contains an access token and refresh token.

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Set the session


    1

    const { data, error } = await supabase.auth.setSession({

    2

        access_token,

    3

        refresh_token

    4

      })

Response

Notes

* * *

## Exchange an auth code for a session

`exchangeCodeForSession(authCode)`

Log in an existing user by exchanging an Auth Code issued during the PKCE flow.

  * Used when `flowType` is set to `pkce` in client options.


### Parameters

  * authCodestring


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Exchange Auth Code


    1

    supabase.auth.exchangeCodeForSession('34e770dd-9ff9-416c-87fa-43b31d7ef225')

Response

* * *

## Start auto-refresh session (non-browser)

`startAutoRefresh()`

Starts an auto-refresh process in the background. The session is checked every few seconds. Close to the time of expiration a process is started to refresh the session. If refreshing fails it will be retried for as long as necessary.

If you set the GoTrueClientOptions#autoRefreshToken you don't need to call this function, it will be called for you.

On browsers the refresh process works only when the tab/window is in the foreground to conserve resources as well as prevent race conditions and flooding auth with requests. If you call this method any managed visibility change callback will be removed and you must manage visibility changes on your own.

On non-browser platforms the refresh process works _continuously_ in the background, which may not be desirable. You should hook into your platform's foreground indication mechanism and call these methods appropriately to conserve resources.

#stopAutoRefresh

  * Only useful in non-browser environments such as React Native or Electron.
  * The Supabase Auth library automatically starts and stops proactively refreshing the session when a tab is focused or not.
  * On non-browser platforms, such as mobile or desktop apps built with web technologies, the library is not able to effectively determine whether the application is _focused_ or not.
  * To give this hint to the application, you should be calling this method when the app is in focus and calling `supabase.auth.stopAutoRefresh()` when it's out of focus.


### Return Type

Promise<void>

Start and stop auto refresh in React Native


    1

    import { AppState } from 'react-native'

    2

    3

    // make sure you register this only once!

    4

    AppState.addEventListener('change', (state) => {

    5

      if (state === 'active') {

    6

        supabase.auth.startAutoRefresh()

    7

      } else {

    8

        supabase.auth.stopAutoRefresh()

    9

      }

    10

    })

* * *

## Stop auto-refresh session (non-browser)

`stopAutoRefresh()`

Stops an active auto refresh process running in the background (if any).

If you call this method any managed visibility change callback will be removed and you must manage visibility changes on your own.

See #startAutoRefresh for more details.

  * Only useful in non-browser environments such as React Native or Electron.
  * The Supabase Auth library automatically starts and stops proactively refreshing the session when a tab is focused or not.
  * On non-browser platforms, such as mobile or desktop apps built with web technologies, the library is not able to effectively determine whether the application is _focused_ or not.
  * When your application goes in the background or out of focus, call this method to stop the proactive refreshing of the session.


### Return Type

Promise<void>

Start and stop auto refresh in React Native


    1

    import { AppState } from 'react-native'

    2

    3

    // make sure you register this only once!

    4

    AppState.addEventListener('change', (state) => {

    5

      if (state === 'active') {

    6

        supabase.auth.startAutoRefresh()

    7

      } else {

    8

        supabase.auth.stopAutoRefresh()

    9

      }

    10

    })

* * *

## Initialize client session

`initialize()`

Initializes the client session either from the url or from storage. This method is automatically called when instantiating the client, but should also be called manually when checking for an error from an auth redirect (oauth, magiclink, password recovery, etc).

### Return Type

Promise<InitializeResult>

Details

* * *

## Auth MFA

This section contains methods commonly used for Multi-Factor Authentication (MFA) and are invoked behind the `supabase.auth.mfa` namespace.

Currently, there is support for time-based one-time password (TOTP) and phone verification code as the 2nd factor. Recovery codes are not supported but users can enroll multiple factors, with an upper limit of 10.

Having a 2nd factor for recovery frees the user of the burden of having to store their recovery codes somewhere. It also reduces the attack surface since multiple recovery codes are usually generated compared to just having 1 backup factor.

Learn more about implementing MFA in your application [in the MFA guide](https://supabase.com/docs/guides/auth/auth-mfa#overview).

* * *

## Enroll a factor

`enroll(params)`

Starts the enrollment process for a new Multi-Factor Authentication (MFA) factor. This method creates a new `unverified` factor. To verify a factor, present the QR code or secret to the user and ask them to add it to their authenticator app. The user has to enter the code from their authenticator app to verify it.

Upon verifying a factor, all other sessions are logged out and the current session's authenticator level is promoted to `aal2`.

  * Use `totp` or `phone` as the `factorType` and use the returned `id` to create a challenge.
  * To create a challenge, see [`mfa.challenge()`](/docs/reference/javascript/auth-mfa-challenge).
  * To verify a challenge, see [`mfa.verify()`](/docs/reference/javascript/auth-mfa-verify).
  * To create and verify a TOTP challenge in a single step, see [`mfa.challengeAndVerify()`](/docs/reference/javascript/auth-mfa-challengeandverify).
  * To generate a QR code for the `totp` secret in Next.js, you can do the following:




    1

    <Image src={data.totp.qr_code} alt={data.totp.uri} layout="fill"></Image>

  * The `challenge` and `verify` steps are separated when using Phone factors as the user will need time to receive and input the code obtained from the SMS in challenge.


### Parameters

  * paramsOne of the following options

Details

    * Option 1MFAEnrollTOTPParams

    * Option 2MFAEnrollPhoneParams

    * Option 3MFAEnrollParamsBase

    * Option 4MFAEnrollWebauthnParams


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Enroll a time-based, one-time password (TOTP) factorEnroll a Phone Factor


    1

    const { data, error } = await supabase.auth.mfa.enroll({

    2

      factorType: 'totp',

    3

      friendlyName: 'your_friendly_name'

    4

    })

    5

    6

    // Use the id to create a challenge.

    7

    // The challenge can be verified by entering the code generated from the authenticator app.

    8

    // The code will be generated upon scanning the qr_code or entering the secret into the authenticator app.

    9

    const { id, type, totp: { qr_code, secret, uri }, friendly_name } = data

    10

    const challenge = await supabase.auth.mfa.challenge({ factorId: id });

Response

* * *

## Create a challenge

`challenge(params)`

Prepares a challenge used to verify that a user has access to a MFA factor.

  * An [enrolled factor](/docs/reference/javascript/auth-mfa-enroll) is required before creating a challenge.
  * To verify a challenge, see [`mfa.verify()`](/docs/reference/javascript/auth-mfa-verify).
  * A phone factor sends a code to the user upon challenge. The channel defaults to `sms` unless otherwise specified.


### Parameters

  * paramsOne of the following options

Details

    * Option 1MFAChallengeParamsBase

    * Option 2MFAChallengePhoneParams

    * Option 3MFAChallengeWebauthnParams

    * Option 4MFAChallengeTOTPParams


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Create a challenge for a factorCreate a challenge for a phone factorCreate a challenge for a phone factor (WhatsApp)


    1

    const { data, error } = await supabase.auth.mfa.challenge({

    2

      factorId: '34e770dd-9ff9-416c-87fa-43b31d7ef225'

    3

    })

Response

* * *

## Verify a challenge

`verify(params)`

Verifies a code against a challenge. The verification code is provided by the user by entering a code seen in their authenticator app.

  * To verify a challenge, please [create a challenge](/docs/reference/javascript/auth-mfa-challenge) first.


### Parameters

  * paramsOne of the following options

Details

    * Option 1MFAVerifyTOTPParams

    * Option 2MFAVerifyPhoneParams

    * Option 3MFAVerifyWebauthnParams


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Verify a challenge for a factor


    1

    const { data, error } = await supabase.auth.mfa.verify({

    2

      factorId: '34e770dd-9ff9-416c-87fa-43b31d7ef225',

    3

      challengeId: '4034ae6f-a8ce-4fb5-8ee5-69a5863a7c15',

    4

      code: '123456'

    5

    })

Response

* * *

## Create and verify a challenge

`challengeAndVerify(params)`

Helper method which creates a challenge and immediately uses the given code to verify against it thereafter. The verification code is provided by the user by entering a code seen in their authenticator app.

  * Intended for use with only TOTP factors.
  * An [enrolled factor](/docs/reference/javascript/auth-mfa-enroll) is required before invoking `challengeAndVerify()`.
  * Executes [`mfa.challenge()`](/docs/reference/javascript/auth-mfa-challenge) and [`mfa.verify()`](/docs/reference/javascript/auth-mfa-verify) in a single step.


### Parameters

  * paramsMFAChallengeAndVerifyTOTPParams


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Create and verify a challenge for a factor


    1

    const { data, error } = await supabase.auth.mfa.challengeAndVerify({

    2

      factorId: '34e770dd-9ff9-416c-87fa-43b31d7ef225',

    3

      code: '123456'

    4

    })

Response

* * *

## Unenroll a factor

`unenroll(params)`

Unenroll removes a MFA factor. A user has to have an `aal2` authenticator level in order to unenroll a `verified` factor.

### Parameters

  * paramsMFAUnenrollParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Unenroll a factor


    1

    const { data, error } = await supabase.auth.mfa.unenroll({

    2

      factorId: '34e770dd-9ff9-416c-87fa-43b31d7ef225',

    3

    })

Response

* * *

## Get Authenticator Assurance Level

`getAuthenticatorAssuranceLevel(jwt?)`

Returns the Authenticator Assurance Level (AAL) for the active session.

  * `aal1` (or `null`) means that the user's identity has been verified only with a conventional login (email+password, OTP, magic link, social login, etc.).
  * `aal2` means that the user's identity has been verified both with a conventional login and at least one MFA factor.


When called without a JWT parameter, this method is fairly quick (microseconds) and rarely uses the network. When a JWT is provided (useful in server-side environments like Edge Functions where no session is stored), this method will make a network request to validate the user and fetch their MFA factors.

  * Authenticator Assurance Level (AAL) is the measure of the strength of an authentication mechanism.
  * In Supabase, having an AAL of `aal1` refers to having the 1st factor of authentication such as an email and password or OAuth sign-in while `aal2` refers to the 2nd factor of authentication such as a time-based, one-time-password (TOTP) or Phone factor.
  * If the user has a verified factor, the `nextLevel` field will return `aal2`, else, it will return `aal1`.
  * An optional `jwt` parameter can be passed to check the AAL level of a specific JWT instead of the current session.


### Parameters

  * jwt

Optional

string

Takes in an optional access token JWT. If no JWT is provided, the JWT from the current session is used.


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Get the AAL details of a sessionGet the AAL details for a specific JWT


    1

    const { data, error } = await supabase.auth.mfa.getAuthenticatorAssuranceLevel()

    2

    const { currentLevel, nextLevel, currentAuthenticationMethods } = data

Response

* * *

## List all factors for current user

`listFactors()`

Returns the list of MFA factors enabled for this user.

### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Auth Passkey

This section contains methods for WebAuthn passkey registration, authentication, and management. Methods are invoked behind the `supabase.auth.passkey` namespace.

Passkey support is an experimental feature. Enable it when creating the client:


    1

    const supabase = createClient(supabaseUrl, publishableKey, {

    2

      auth: {

    3

        experimental: { passkey: true },

    4

      },

    5

    })

* * *

## List passkeys

`list()`

Lists all passkeys registered for the currently signed-in user.

### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Update a passkey

`update(params)`

Updates a passkey's friendly name.

### Parameters

  * paramsPasskeyUpdateParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Delete a passkey

`delete(params)`

Deletes a passkey for the currently signed-in user.

### Parameters

  * paramsPasskeyDeleteParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Start passkey registration

`startRegistration()`

Starts the passkey registration ceremony. Fetches a registration challenge and credential creation options from the server. Used as the first step of a two-step registration flow when the caller wants to handle `navigator.credentials.create()` themselves.

### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Verify passkey registration

`verifyRegistration(params)`

Verifies a passkey registration credential against a previously issued challenge. Used as the second step of a two-step registration flow.

### Parameters

  * paramsVerifyPasskeyRegistrationParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Start passkey authentication

`startAuthentication(params?)`

Starts the passkey authentication ceremony. Fetches an authentication challenge and credential request options from the server. Used as the first step of a two-step sign-in flow when the caller wants to handle `navigator.credentials.get()` themselves.

### Parameters

  * params

Optional

StartPasskeyAuthenticationParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Verify passkey authentication

`verifyAuthentication(params)`

Verifies a passkey authentication credential against a previously issued challenge. Used as the second step of a two-step sign-in flow.

### Parameters

  * paramsVerifyPasskeyAuthenticationParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## OAuth Server

The OAuth Server API allows you to build custom OAuth consent screens for your application. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth.

* * *

## Get authorization details

`getAuthorizationDetails(authorizationId)`

Retrieves details about an OAuth authorization request. Used to display consent information to the user. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth.

This method returns one of two response types:

  * `OAuthAuthorizationDetails`: User needs to consent - show consent page with client info
  * `OAuthRedirect`: User already consented - redirect immediately to the OAuth client


Use type narrowing to distinguish between the responses:


    1

    if ('authorization_id' in data) {

    2

      // Show consent page

    3

    } else {

    4

      // Redirect to data.redirect_url

    5

    }

### Parameters

  * authorizationIdstring

The authorization ID from the authorization request


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Approve authorization

`approveAuthorization(authorizationId, options?)`

Approves an OAuth authorization request. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth.

After approval, the user's consent is stored and an authorization code is generated. The response contains a complete redirect URL with the authorization code and state.

### Parameters

  * authorizationIdstring

The authorization ID to approve

  * options

Optional

object

Optional parameters

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Deny authorization

`denyAuthorization(authorizationId, options?)`

Denies an OAuth authorization request. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth.

After denial, the response contains a redirect URL with an OAuth error (access_denied) to inform the OAuth client that the user rejected the request.

### Parameters

  * authorizationIdstring

The authorization ID to deny

  * options

Optional

object

Optional parameters

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## List grants

`listGrants()`

Lists all OAuth grants that the authenticated user has authorized. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth.

### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Revoke grant

`revokeGrant(options)`

Revokes a user's OAuth grant for a specific client. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth.

Revocation marks consent as revoked, deletes active sessions for that OAuth client, and invalidates associated refresh tokens.

### Parameters

  * optionsobject

Revocation options

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Auth Admin

  * Any method under the `supabase.auth.admin` namespace requires a `secret` key.
  * These methods are considered admin methods and should be called on a trusted server. Never expose your `secret` key in the browser.


Create server-side auth client


    1

    import { createClient } from '@supabase/supabase-js'

    2

    3

    const supabase = createClient(supabase_url, secret_key, {

    4

      auth: {

    5

        autoRefreshToken: false,

    6

        persistSession: false

    7

      }

    8

    })

    9

    10

    // Access auth admin api

    11

    const adminAuthClient = supabase.auth.admin

* * *

## Retrieve a user

`getUserById(uid)`

Get user by id.

  * Fetches the user object from the database based on the user's id.
  * The `getUserById()` method requires the user's id which maps to the `auth.users.id` column.


### Parameters

  * uidstring

The user's unique identifier

This function should only be called on a server. Never expose your `service_role` key in the browser.


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Fetch the user object using the access_token jwt


    1

    const { data, error } = await supabase.auth.admin.getUserById(1)

Response

* * *

## List all users

`listUsers(params?)`

Get a list of users.

This function should only be called on a server. Never expose your `service_role` key in the browser.

  * Defaults to return 50 users per page.


### Parameters

  * params

Optional

PageParams

An object which supports `page` and `perPage` as numbers, to alter the paginated results.

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Get a page of usersPaginated list of users


    1

    const { data: { users }, error } = await supabase.auth.admin.listUsers()

* * *

## Create a user

`createUser(attributes)`

Creates a new user. This function should only be called on a server. Never expose your `service_role` key in the browser.

  * To confirm the user's email address or phone number, set `email_confirm` or `phone_confirm` to true. Both arguments default to false.
  * `createUser()` will not send a confirmation email to the user. You can use [`inviteUserByEmail()`](/docs/reference/javascript/auth-admin-inviteuserbyemail) if you want to send them an email invite instead.
  * If you are sure that the created user's email or phone number is legitimate and verified, you can set the `email_confirm` or `phone_confirm` param to `true`.


### Parameters

  * attributesAdminUserAttributes

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


With custom user metadataAuto-confirm the user's emailAuto-confirm the user's phone number


    1

    const { data, error } = await supabase.auth.admin.createUser({

    2

      email: 'user@email.com',

    3

      password: 'password',

    4

      user_metadata: { name: 'Yoda' }

    5

    })

Response

* * *

## Delete a user

`deleteUser(id, shouldSoftDelete)`

Delete a user. Requires a `service_role` key.

  * The `deleteUser()` method requires the user's ID, which maps to the `auth.users.id` column.


### Parameters

  * idstring

The user id you want to remove.

  * shouldSoftDeleteboolean

If true, then the user will be soft-deleted from the auth schema. Soft deletion allows user identification from the hashed user ID but is not reversible. Defaults to false for backward compatibility.

This function should only be called on a server. Never expose your `service_role` key in the browser.


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Removes a user


    1

    const { data, error } = await supabase.auth.admin.deleteUser(

    2

      '715ed5db-f090-4b8c-a067-640ecee36aa0'

    3

    )

Response

* * *

## Send an email invite link

`inviteUserByEmail(email, options)`

Sends an invite link to an email address.

  * Sends an invite link to the user's email address.
  * The `inviteUserByEmail()` method is typically used by administrators to invite users to join the application.
  * Note that PKCE is not supported when using `inviteUserByEmail`. This is because the browser initiating the invite is often different from the browser accepting the invite which makes it difficult to provide the security guarantees required of the PKCE flow.


### Parameters

  * emailstring

The email address of the user.

  * optionsobject

Additional options to be included when inviting.

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Invite a user


    1

    const { data, error } = await supabase.auth.admin.inviteUserByEmail('email@example.com')

Response

* * *

## Generate an email link

`generateLink(params)`

Generates email links and OTPs to be sent via a custom email provider.

  * The following types can be passed into `generateLink()`: `signup`, `magiclink`, `invite`, `recovery`, `email_change_current`, `email_change_new`, `phone_change`.
  * `generateLink()` only generates the email link for `email_change_email` if the **Secure email change** is enabled in your project's [email auth provider settings](/dashboard/project/_/auth/providers).
  * `generateLink()` handles the creation of the user for `signup`, `invite` and `magiclink`.


### Parameters

  * paramsOne of the following options

Details

    * Option 1GenerateSignupLinkParams

Details

    * Option 2GenerateInviteOrMagiclinkParams

Details

    * Option 3GenerateRecoveryLinkParams

Details

    * Option 4GenerateEmailChangeLinkParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Generate a signup linkGenerate an invite linkGenerate a magic linkGenerate a recovery linkGenerate links to change current email address


    1

    const { data, error } = await supabase.auth.admin.generateLink({

    2

      type: 'signup',

    3

      email: 'email@example.com',

    4

      password: 'secret'

    5

    })

Response

* * *

## Update a user

`updateUserById(uid, attributes)`

Updates the user data. Changes are applied directly without confirmation flows.

**Important:** This is a server-side operation and does **not** trigger client-side `onAuthStateChange` listeners. The admin API has no connection to client state.

To sync changes to the client after calling this method:

  1. On the client, call `supabase.auth.refreshSession()` to fetch the updated user data
  2. This will trigger the `TOKEN_REFRESHED` event and notify all listeners


### Parameters

  * uidstring

The user's unique identifier

  * attributesAdminUserAttributes

The data you want to update.

This function should only be called on a server. Never expose your `service_role` key in the browser.

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Example 1Updates a user's emailUpdates a user's passwordUpdates a user's metadataUpdates a user's app_metadataConfirms a user's email addressConfirms a user's phone numberBan a user for 100 years


    1

    // Server-side (Edge Function)

    2

    const { data, error } = await supabase.auth.admin.updateUserById(

    3

      userId,

    4

      { user_metadata: { preferences: { theme: 'dark' } } }

    5

    )

    6

    7

    // Client-side (to sync the changes)

    8

    const { data, error } = await supabase.auth.refreshSession()

    9

    // onAuthStateChange listeners will now be notified with updated user

* * *

## Sign out a user (admin)

`signOut(jwt, scope)`

Removes a logged-in session.

### Parameters

  * jwtstring

A valid, logged-in JWT.

  * scopeOne of the following options

The logout sope.

Details

    * Option 1"global"

    * Option 2"local"

    * Option 3"others"


### Return Type

Promise<object>

Details

* * *

## Delete a factor for a user

`deleteFactor(params)`

Deletes a factor on a user. This will log the user out of all active sessions if the deleted factor was verified.

### Parameters

  * paramsAuthMFAAdminDeleteFactorParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Delete a factor for a user


    1

    const { data, error } = await supabase.auth.admin.mfa.deleteFactor({

    2

      id: '34e770dd-9ff9-416c-87fa-43b31d7ef225',

    3

      userId: 'a89baba7-b1b7-440f-b4bb-91026967f66b',

    4

    })

Response

* * *

## List all factors for a user (admin)

`listFactors(params)`

Lists all factors associated to a user.

### Parameters

  * paramsAuthMFAAdminListFactorsParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


List all factors for a user


    1

    const { data, error } = await supabase.auth.admin.mfa.listFactors()

Response

* * *

## Passkey Admin

Contains passkey administration methods. Requires a secret key.

* * *

## List passkeys for a user

`listPasskeys(params)`

Lists all passkeys registered for a specific user.

This function should only be called on a server. Never expose your `service_role` key in the browser.

### Parameters

  * paramsAuthPasskeyAdminListParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Delete a passkey

`deletePasskey(params)`

Deletes a specific passkey for a specific user.

This function should only be called on a server. Never expose your `service_role` key in the browser.

### Parameters

  * paramsAuthPasskeyAdminDeleteParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## OAuth Admin

The OAuth Admin API allows you to manage OAuth clients programmatically. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth. These functions should only be called on a server. Never expose your `secret` key in the browser.

* * *

## List OAuth clients

`listClients(params?)`

Lists all OAuth clients with optional pagination. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth.

This function should only be called on a server. Never expose your `service_role` key in the browser.

### Parameters

  * params

Optional

PageParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Get OAuth client

`getClient(clientId)`

Gets details of a specific OAuth client. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth.

This function should only be called on a server. Never expose your `service_role` key in the browser.

### Parameters

  * clientIdstring


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Create OAuth client

`createClient(params)`

Creates a new OAuth client. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth.

This function should only be called on a server. Never expose your `service_role` key in the browser.

### Parameters

  * paramsCreateOAuthClientParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Update OAuth client

`updateClient(clientId, params)`

Updates an existing OAuth client. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth.

This function should only be called on a server. Never expose your `service_role` key in the browser.

### Parameters

  * clientIdstring

  * paramsUpdateOAuthClientParams

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Delete OAuth client

`deleteClient(clientId)`

Deletes an OAuth client. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth.

This function should only be called on a server. Never expose your `service_role` key in the browser.

### Parameters

  * clientIdstring


### Return Type

Promise<object>

Details

* * *

## Regenerate client secret

`regenerateClientSecret(clientId)`

Regenerates the secret for an OAuth client. Only relevant when the OAuth 2.1 server is enabled in Supabase Auth.

This function should only be called on a server. Never expose your `service_role` key in the browser.

### Parameters

  * clientIdstring


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


* * *

## Invokes a Supabase Edge Function.

`invoke(functionName, options)`

Invokes a function

  * Requires an Authorization header.
  * Invoke params generally match the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) spec.
  * When you pass in a body to your function, we automatically attach the Content-Type header for `Blob`, `ArrayBuffer`, `File`, `FormData` and `String`. If it doesn't match any of these types we assume the payload is `json`, serialize it and attach the `Content-Type` header as `application/json`. You can override this behavior by passing in a `Content-Type` header of your own.
  * Responses are automatically parsed as `json`, `blob` and `form-data` depending on the `Content-Type` header sent by your function. Responses are parsed as `text` by default.


### Parameters

  * functionNamestring

The name of the Function to invoke.

  * optionsFunctionInvokeOptions

Options for invoking the Function.

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1FunctionsResponseSuccess

  * Option 2FunctionsResponseFailure


Example 1Basic invocationError handlingPassing custom headersCalling with DELETE HTTP verbInvoking a Function in the UsEast1 regionCalling with GET HTTP verbStandalone client invoke


    1

    const { data, error } = await functions.invoke('hello-world', {

    2

      body: { name: 'Ada' },

    3

    })

* * *

## CORS headers for Edge Functions

Default CORS headers for Supabase Edge Functions.

Includes all headers sent by Supabase client libraries and allows all standard HTTP methods. Use this for simple CORS configurations with wildcard origin.

Basic usage


    1

    import { corsHeaders } from '@supabase/supabase-js/cors'

    2

    3

    Deno.serve(async (req) => {

    4

      if (req.method === 'OPTIONS') {

    5

        return new Response('ok', { headers: corsHeaders })

    6

      }

    7

    8

      return new Response(

    9

        JSON.stringify({ data: 'Hello' }),

    10

        { headers: { ...corsHeaders, 'Content-Type': 'application/json' } }

    11

      )

    12

    })

* * *

## Update authorization token

`setAuth(token)`

Updates the authorization header

### Parameters

  * tokenstring

the new jwt token sent in the authorisation header


### Return Type

void

Setting the authorization header


    1

    functions.setAuth(session.access_token)

* * *

## Subscribe to channel

`on(type, filter, callback)`

Creates an event handler that listens to changes.

  * By default, Broadcast and Presence are enabled for all projects.
  * By default, listening to database changes is disabled for new projects due to database performance and security concerns. You can turn it on by managing Realtime's [replication](/docs/guides/api#realtime-api-overview).
  * You can receive the "previous" data for updates and deletes by setting the table's `REPLICA IDENTITY` to `FULL` (e.g., `ALTER TABLE your_table REPLICA IDENTITY FULL;`).
  * Row level security is not applied to delete statements. When RLS is enabled and replica identity is set to full, only the primary key is sent to clients.


### Parameters

  * typeOne of the following options

Details

    * Option 1"presence"

    * Option 2"postgres_changes"

    * Option 3"broadcast"

    * Option 4"system"

  * filterOne of the following options

Details

    * Option 1object

Details

    * Option 2object

Details

    * Option 3object

Details

    * Option 4object

Details

    * Option 5RealtimePostgresChangesFilter

Details

    * Option 6RealtimePostgresChangesFilter

Details

    * Option 7RealtimePostgresChangesFilter

Details

    * Option 8RealtimePostgresChangesFilter

Details

    * Option 9RealtimePostgresChangesFilter

Details

    * Option 10object

Details

    * Option 11object

Details

    * Option 12object

Details

    * Option 13object

Details

    * Option 14object

Details

  * callbackfunction

Details


Listen to broadcast messagesListen to presence syncListen to presence joinListen to presence leaveListen to all database changesListen to a specific tableListen to insertsListen to updatesListen to deletesListen to multiple eventsListen to row level changes


    1

    const channel = supabase.channel("room1")

    2

    3

    channel.on("broadcast", { event: "cursor-pos" }, (payload) => {

    4

      console.log("Cursor position received!", payload);

    5

    }).subscribe((status) => {

    6

      if (status === "SUBSCRIBED") {

    7

        channel.send({

    8

          type: "broadcast",

    9

          event: "cursor-pos",

    10

          payload: { x: Math.random(), y: Math.random() },

    11

        });

    12

      }

    13

    });

* * *

## Unsubscribe from a channel

`removeChannel(channel)`

Unsubscribes and removes Realtime channel from Realtime client.

  * Removing a channel is a great way to maintain the performance of your project's Realtime service as well as your database if you're listening to Postgres changes. Supabase will automatically handle cleanup 30 seconds after a client is disconnected, but unused channels may cause degradation as more clients are simultaneously subscribed.


### Parameters

  * channelRealtimeChannel

The name of the Realtime channel.


### Return Type

Promise<One of the following options>

Details

  * Option 1"ok"

  * Option 2"timed out"

  * Option 3"error"

  * Option 4


Removes a channel


    1

    supabase.removeChannel(myChannel)

* * *

## Unsubscribe from all channels

`removeAllChannels()`

Unsubscribes and removes all Realtime channels from Realtime client.

  * Removing channels is a great way to maintain the performance of your project's Realtime service as well as your database if you're listening to Postgres changes. Supabase will automatically handle cleanup 30 seconds after a client is disconnected, but unused channels may cause degradation as more clients are simultaneously subscribed.


### Return Type

Promise<Array<One of the following options>>

Details

Remove all channels


    1

    supabase.removeAllChannels()

* * *

## Retrieve all channels

`getChannels()`

Returns all Realtime channels.

### Return Type

Array<RealtimeChannel>

Get all channels


    1

    const channels = supabase.getChannels()

* * *

## Broadcast a message

`send(args, opts)`

Sends a message into the channel.

  * When using REST you don't need to subscribe to the channel
  * REST calls are only available from 2.37.0 onwards
  * If you create a channel only to send a REST broadcast, remove it from the client when the send completes


### Parameters

  * argsobject

Arguments to send to channel

Details

  * opts{ [key: string]: any }

Options to be used during the send process


### Return Type

Promise<One of the following options>

Details

  * Option 1"ok"

  * Option 2"timed out"

  * Option 3"error"

  * Option 4


Send a message via websocketSend a message via REST


    1

    const channel = supabase.channel('room1')

    2

    3

    channel.subscribe((status) => {

    4

      if (status === 'SUBSCRIBED') {

    5

        channel.send({

    6

          type: 'broadcast',

    7

          event: 'cursor-pos',

    8

          payload: { x: Math.random(), y: Math.random() },

    9

        })

    10

      }

    11

    })

Response

* * *

## Set authentication token

`setAuth(token)`

Sets the JWT access token used for channel subscription authorization and Realtime RLS.

If param is null it will use the `accessToken` callback function or the token set on the client.

On callback used, it will set the value of the token internal to the client.

When a token is explicitly provided, it will be preserved across channel operations (including removeChannel and resubscribe). The `accessToken` callback will not be invoked until `setAuth()` is called without arguments.

### Parameters

  * tokenOne of the following options

A JWT string to override the token set on the client.

Details

    * Option 1null

    * Option 2string


### Return Type

Promise<void>

Example 1


    1

    Setting the authorization header

    2

    // Use a manual token (preserved across resubscribes, ignores accessToken callback)

    3

    client.realtime.setAuth('my-custom-jwt')

    4

    5

    // Switch back to using the accessToken callback

    6

    client.realtime.setAuth()

* * *

## File Buckets

This section contains methods for working with File Buckets.

* * *

## Access a storage bucket

`from(id)`

Perform file operation in a bucket.

### Parameters

  * idstring

The bucket id to operate on.


Accessing a bucket


    1

    const avatars = supabase.storage.from('avatars')

* * *

## List all buckets

`listBuckets(options?)`

Retrieves the details of all Storage buckets within an existing project.

  * RLS policy permissions required:
    * `buckets` table permissions: `select`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * options

Optional

ListBucketOptions

Query parameters for listing buckets

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


List bucketsList buckets with options


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .listBuckets()

* * *

## Retrieve a bucket

`getBucket(id)`

Retrieves the details of an existing Storage bucket.

  * RLS policy permissions required:
    * `buckets` table permissions: `select`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * idstring

The unique identifier of the bucket you would like to retrieve.


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Get bucket


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .getBucket('avatars')

Response

* * *

## Create a bucket

`createBucket(id, options)`

Creates a new Storage bucket

  * RLS policy permissions required:
    * `buckets` table permissions: `insert`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * idstring

A unique identifier for the bucket you are creating.

  * optionsobject

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Create bucket


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .createBucket('avatars', {

    4

        public: false,

    5

        allowedMimeTypes: ['image/png'],

    6

        fileSizeLimit: 1024

    7

      })

Response

* * *

## Empty a bucket

`emptyBucket(id)`

Removes all objects inside a single bucket.

  * RLS policy permissions required:
    * `buckets` table permissions: `select`
    * `objects` table permissions: `select` and `delete`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * idstring

The unique identifier of the bucket you would like to empty.


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Empty bucket


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .emptyBucket('avatars')

Response

* * *

## Update a bucket

`updateBucket(id, options)`

Updates a Storage bucket

  * RLS policy permissions required:
    * `buckets` table permissions: `select` and `update`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * idstring

A unique identifier for the bucket you are updating.

  * optionsobject

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Update bucket


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .updateBucket('avatars', {

    4

        public: false,

    5

        allowedMimeTypes: ['image/png'],

    6

        fileSizeLimit: 1024

    7

      })

Response

* * *

## Delete a bucket

`deleteBucket(id)`

Deletes an existing bucket. A bucket can't be deleted with existing objects inside it. You must first `empty()` the bucket.

  * RLS policy permissions required:
    * `buckets` table permissions: `select` and `delete`
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * idstring

The unique identifier of the bucket you would like to delete.


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Delete bucket


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .deleteBucket('avatars')

Response

* * *

## Upload a file

`upload(path, fileBody, fileOptions?)`

Uploads a file to an existing bucket.

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: only `insert` when you are uploading new files and `select`, `insert` and `update` when you are upserting files
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works
  * For React Native, using either `Blob`, `File` or `FormData` does not work as intended. Upload file using `ArrayBuffer` from base64 file data instead, see example below.


### Parameters

  * pathstring

The file path, including the file name. Should be of the format `folder/subfolder/filename.png`. The bucket must already exist before attempting to upload.

  * fileBodyFileBody

The body of the file to be stored in the bucket.

  * fileOptions

Optional

FileOptions

Optional file upload options including cacheControl, contentType, upsert, and metadata.

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Upload fileUpload file using `ArrayBuffer` from base64 file data


    1

    const avatarFile = event.target.files[0]

    2

    const { data, error } = await supabase

    3

      .storage

    4

      .from('avatars')

    5

      .upload('public/avatar1.png', avatarFile, {

    6

        cacheControl: '3600',

    7

        upsert: false

    8

      })

Response

* * *

## Replace an existing file

`update(path, fileBody, fileOptions?)`

Replaces an existing file at the specified path with a new one.

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `update` and `select`
  * `update()` always replaces the file at the given path regardless of the `upsert` option.
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works
  * For React Native, using either `Blob`, `File` or `FormData` does not work as intended. Update file using `ArrayBuffer` from base64 file data instead, see example below.


### Parameters

  * pathstring

The relative file path. Should be of the format `folder/subfolder/filename.png`. The bucket must already exist before attempting to update.

  * fileBodyOne of the following options

The body of the file to be stored in the bucket.

Details

    * Option 1string

    * Option 2ArrayBuffer

    * Option 3ReadableStream

    * Option 4Blob

    * Option 5File

    * Option 6FormData

    * Option 7@types/node.__global.NodeJS.ReadableStream

    * Option 8URLSearchParams

    * Option 9ArrayBufferView

    * Option 10@types/node.__global.Buffer

  * fileOptions

Optional

FileOptions

Optional file upload options including cacheControl, contentType, and metadata. **Note:** The `upsert` option has no effect here. `update()` always replaces the file at the given path, so the `x-upsert` header is not sent. To control upsert behavior, use `upload()` instead.

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Update fileUpdate file using `ArrayBuffer` from base64 file data


    1

    const avatarFile = event.target.files[0]

    2

    const { data, error } = await supabase

    3

      .storage

    4

      .from('avatars')

    5

      .update('public/avatar1.png', avatarFile, {

    6

        cacheControl: '3600'

    7

      })

Response

* * *

## Move an existing file

`move(fromPath, toPath, options?)`

Moves an existing file to a new path in the same bucket.

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `update` and `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * fromPathstring

The original file path, including the current file name. For example `folder/image.png`.

  * toPathstring

The new file path, including the new file name. For example `folder/image-new.png`.

  * options

Optional

DestinationOptions

The destination options.

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Move file


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .from('avatars')

    4

      .move('public/avatar1.png', 'private/avatar2.png')

Response

* * *

## Copy an existing file

`copy(fromPath, toPath, options?)`

Copies an existing file to a new path in the same bucket.

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `insert` and `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * fromPathstring

The original file path, including the current file name. For example `folder/image.png`.

  * toPathstring

The new file path, including the new file name. For example `folder/image-copy.png`.

  * options

Optional

DestinationOptions

The destination options.

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Copy file


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .from('avatars')

    4

      .copy('public/avatar1.png', 'private/avatar2.png')

Response

* * *

## Create a signed URL

`createSignedUrl(path, expiresIn, options?)`

Creates a signed URL. Use a signed URL to share a file for a fixed amount of time.

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * pathstring

The file path, including the current file name. For example `folder/image.png`.

  * expiresInnumber

The number of seconds until the signed URL expires. For example, `60` for a URL which is valid for one minute.

  * options

Optional

object

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Create Signed URLCreate a signed URL for an asset with transformationsCreate a signed URL which triggers the download of the asset


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .from('avatars')

    4

      .createSignedUrl('folder/avatar1.png', 60)

Response

* * *

## Create signed URLs

`createSignedUrls(paths, expiresIn, options?)`

Creates multiple signed URLs. Use a signed URL to share a file for a fixed amount of time.

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * pathsArray<string>

The file paths to be downloaded, including the current file names. For example `['folder/image.png', 'folder2/image2.png']`.

  * expiresInnumber

The number of seconds until the signed URLs expire. For example, `60` for URLs which are valid for one minute.

  * options

Optional

object

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Create Signed URLs


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .from('avatars')

    4

      .createSignedUrls(['folder/avatar1.png', 'folder/avatar2.png'], 60)

Response

* * *

## Create signed upload URL

`createSignedUploadUrl(path, options?)`

Creates a signed upload URL. Signed upload URLs can be used to upload files to the bucket without further authentication. They are valid for 2 hours.

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `insert`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * pathstring

The file path, including the current file name. For example `folder/image.png`.

  * options

Optional

object

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Create Signed Upload URL


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .from('avatars')

    4

      .createSignedUploadUrl('folder/cat.jpg')

Response

* * *

## Upload to a signed URL

`uploadToSignedUrl(path, token, fileBody, fileOptions?)`

Upload a file with a token generated from `createSignedUploadUrl`.

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * pathstring

The file path, including the file name. Should be of the format `folder/subfolder/filename.png`. The bucket must already exist before attempting to upload.

  * tokenstring

The token generated from `createSignedUploadUrl`

  * fileBodyFileBody

The body of the file to be stored in the bucket.

  * fileOptions

Optional

FileOptions

HTTP headers (cacheControl, contentType, etc.). **Note:** The `upsert` option has no effect here. To enable upsert behavior, pass `{ upsert: true }` when calling `createSignedUploadUrl()` instead.

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Upload to a signed URL


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .from('avatars')

    4

      .uploadToSignedUrl('folder/cat.jpg', 'token-from-createSignedUploadUrl', file)

Response

* * *

## Retrieve public URL

`getPublicUrl(path, options?)`

A simple convenience function to get the URL for an asset in a public bucket. If you do not want to use this function, you can construct the public URL by concatenating the bucket URL with the path to the asset. This function does not verify if the bucket is public. If a public URL is created for a bucket which is not public, you will not be able to download the asset.

  * The bucket needs to be set to public, either via [updateBucket()](/docs/reference/javascript/storage-updatebucket) or by going to Storage on [supabase.com/dashboard](https://supabase.com/dashboard), clicking the overflow menu on a bucket and choosing "Make public"
  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: none
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * pathstring

The path and name of the file to generate the public URL for. For example `folder/image.png`.

  * options

Optional

object

Details


### Return Type

object

Details

Returns the URL for an asset in a public bucketReturns the URL for an asset in a public bucket with transformationsReturns the URL which triggers the download of an asset in a public bucket


    1

    const { data } = supabase

    2

      .storage

    3

      .from('public-bucket')

    4

      .getPublicUrl('folder/avatar1.png')

Response

* * *

## Download a file

`download(path, options?, parameters?)`

Downloads a file from a private bucket. For public buckets, make a request to the URL returned from `getPublicUrl` instead.

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * pathstring

The full path and file name of the file to be downloaded. For example `folder/image.png`.

  * options

Optional

Options

  * parameters

Optional

FetchParameters

Additional fetch parameters like signal for cancellation. Supports standard fetch options including cache control.

Details


Download fileDownload file with transformationsDownload with cache control (useful in Edge Functions)Download with abort signal


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .from('avatars')

    4

      .download('folder/avatar1.png')

Response

* * *

## Delete files in a bucket

`remove(paths)`

Deletes files within the same bucket

Returns an array of FileObject entries for the deleted files. Note that deprecated fields like `bucket_id` may or may not be present in the response - do not rely on them.

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `delete` and `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * pathsArray<string>

An array of files to delete, including the path and file name. For example [`'folder/image.png'`].


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Delete file


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .from('avatars')

    4

      .remove(['folder/avatar1.png'])

Response

* * *

## List all files in a bucket

`list(path?, options?, parameters?)`

Lists all the files and folders within a path of the bucket.

**Important:** For folder entries, fields like `id`, `updated_at`, `created_at`, `last_accessed_at`, and `metadata` will be `null`. Only files have these fields populated. Additionally, deprecated fields like `bucket_id`, `owner`, and `buckets` are NOT returned by this method.

  * RLS policy permissions required:
    * `buckets` table permissions: none
    * `objects` table permissions: `select`
  * Refer to the [Storage guide](/docs/guides/storage/security/access-control) on how access control works


### Parameters

  * path

Optional

string

The folder path.

  * options

Optional

SearchOptions

Search options including limit (defaults to 100), offset, sortBy, and search

Details

  * parameters

Optional

FetchParameters

Optional fetch parameters including signal for cancellation

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


List files in a bucketSearch files in a bucket


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .from('avatars')

    4

      .list('folder', {

    5

        limit: 100,

    6

        offset: 0,

    7

        sortBy: { column: 'name', order: 'asc' },

    8

      })

    9

    10

    // Handle files vs folders

    11

    data?.forEach(item => {

    12

      if (item.id !== null) {

    13

        // It's a file

    14

        console.log('File:', item.name, 'Size:', item.metadata?.size)

    15

      } else {

    16

        // It's a folder

    17

        console.log('Folder:', item.name)

    18

      }

    19

    })

Response

* * *

## Check if file exists

`exists(path)`

Checks the existence of a file.

### Parameters

  * pathstring

The file path, including the file name. For example `folder/image.png`.


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Check file existence


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .from('avatars')

    4

      .exists('folder/avatar1.png')

* * *

## Get file metadata

`info(path)`

Retrieves the details of an existing file.

Returns detailed file metadata including size, content type, and timestamps. Note: The API returns `last_modified` field, not `updated_at`.

### Parameters

  * pathstring

The file path, including the file name. For example `folder/image.png`.


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Get file info


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .from('avatars')

    4

      .info('folder/avatar1.png')

    5

    6

    if (data) {

    7

      console.log('Last modified:', data.lastModified)

    8

      console.log('Size:', data.size)

    9

    }

* * *

## List files (v2)

`listV2(options?, parameters?)`

Lists all the files and folders within a bucket using the V2 API with pagination support.

**Important:** Folder entries in the `folders` array only contain `name` and optionally `key` — they have no `id`, timestamps, or `metadata` fields. Full file metadata is only available on entries in the `objects` array.

this method signature might change in the future

### Parameters

  * options

Optional

SearchV2Options

Search options including prefix, cursor for pagination, limit, with_delimiter

Details

  * parameters

Optional

FetchParameters

Optional fetch parameters including signal for cancellation

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


List files with pagination


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .from('avatars')

    4

      .listV2({

    5

        prefix: 'folder/',

    6

        limit: 100,

    7

      })

    8

    9

    // Handle pagination

    10

    if (data?.hasNext) {

    11

      const nextPage = await supabase

    12

        .storage

    13

        .from('avatars')

    14

        .listV2({

    15

          prefix: 'folder/',

    16

          cursor: data.nextCursor,

    17

        })

    18

    }

    19

    20

    // Handle files vs folders

    21

    data?.objects.forEach(file => {

    22

      if (file.id !== null) {

    23

        console.log('File:', file.name, 'Size:', file.metadata?.size)

    24

      }

    25

    })

    26

    data?.folders.forEach(folder => {

    27

      console.log('Folder:', folder.name)

    28

    })

* * *

## Convert file to base64

`toBase64(data)`

### Parameters

  * datastring


### Return Type

string

* * *

## Analytics Buckets

This section contains methods for working with Analytics Buckets.

* * *

## Access an analytics bucket

Creates a new StorageAnalyticsClient instance

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * urlstring

The base URL for the storage API

  * headers{ [key: string]: string }

HTTP headers to include in requests

  * fetch

Optional

function

Optional custom fetch implementation

Details


Using supabase-js (recommended)Standalone import for bundle-sensitive environments


    1

    import { createClient } from '@supabase/supabase-js'

    2

    3

    const supabase = createClient('https://xyzcompany.supabase.co', 'your-publishable-key')

    4

    const { data, error } = await supabase.storage.analytics.listBuckets()

* * *

## Create a new analytics bucket

`createBucket(name)`

Creates a new analytics bucket using Iceberg tables Analytics buckets are optimized for analytical queries and data processing

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

  * Creates a new analytics bucket using Iceberg tables
  * Analytics buckets are optimized for analytical queries and data processing


### Parameters

  * namestring

A unique name for the bucket you are creating


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Create analytics bucket


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .analytics

    4

      .createBucket('analytics-data')

Response

* * *

## List analytics buckets

`listBuckets(options?)`

Retrieves the details of all Analytics Storage buckets within an existing project Only returns buckets of type 'ANALYTICS'

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

  * Retrieves the details of all Analytics Storage buckets within an existing project
  * Only returns buckets of type 'ANALYTICS'


### Parameters

  * options

Optional

object

Query parameters for listing buckets

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


List analytics buckets


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .analytics

    4

      .listBuckets({

    5

        limit: 10,

    6

        offset: 0,

    7

        sortColumn: 'created_at',

    8

        sortOrder: 'desc'

    9

      })

Response

* * *

## Delete an analytics bucket

`deleteBucket(bucketName)`

Deletes an existing analytics bucket A bucket can't be deleted with existing objects inside it You must first empty the bucket before deletion

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

  * Deletes an analytics bucket


### Parameters

  * bucketNamestring

The unique identifier of the bucket you would like to delete


### Return Type

Promise<One of the following options>

Details

  * Option 1object

Details

  * Option 2object

Details


Delete analytics bucket


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .analytics

    4

      .deleteBucket('analytics-data')

Response

* * *

## Vector Buckets

This section contains methods for working with Vector Buckets.

* * *

## Access a vector bucket

`from(vectorBucketName)`

Access operations for a specific vector bucket Returns a scoped client for index and vector operations within the bucket

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * vectorBucketNamestring

Name of the vector bucket


Accessing a vector bucket


    1

    const bucket = supabase.storage.vectors.from('embeddings-prod')

* * *

## Create a vector bucket

`createBucket(vectorBucketName)`

Creates a new vector bucket Vector buckets are containers for vector indexes and their data

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * vectorBucketNamestring

Unique name for the vector bucket


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


Creating a vector bucket


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .vectors

    4

      .createBucket('embeddings-prod')

* * *

## Delete a vector bucket

`deleteBucket(vectorBucketName)`

Deletes a vector bucket (bucket must be empty) All indexes must be deleted before deleting the bucket

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * vectorBucketNamestring

Name of the vector bucket to delete


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


Delete a vector bucket


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .vectors

    4

      .deleteBucket('embeddings-old')

* * *

## Retrieve a vector bucket

`getBucket(vectorBucketName)`

Retrieves metadata for a specific vector bucket

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * vectorBucketNamestring

Name of the vector bucket


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


Get bucket metadata


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .vectors

    4

      .getBucket('embeddings-prod')

    5

    6

    console.log('Bucket created:', data?.vectorBucket.creationTime)

* * *

## List all vector buckets

`listBuckets(options)`

Lists all vector buckets with optional filtering and pagination

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * optionsListVectorBucketsOptions

Optional filters (prefix, maxResults, nextToken)

Details


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


List vector buckets


    1

    const { data, error } = await supabase

    2

      .storage

    3

      .vectors

    4

      .listBuckets({ prefix: 'embeddings-' })

    5

    6

    data?.vectorBuckets.forEach(bucket => {

    7

      console.log(bucket.vectorBucketName)

    8

    })

* * *

## Create a vector index

`createIndex(options)`

Creates a new vector index in this bucket Convenience method that automatically includes the bucket name

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * optionsOmit

Index configuration (vectorBucketName is automatically set)


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


Creating a vector index


    1

    const bucket = supabase.storage.vectors.from('embeddings-prod')

    2

    await bucket.createIndex({

    3

      indexName: 'documents-openai',

    4

      dataType: 'float32',

    5

      dimension: 1536,

    6

      distanceMetric: 'cosine',

    7

      metadataConfiguration: {

    8

        nonFilterableMetadataKeys: ['raw_text']

    9

      }

    10

    })

* * *

## Delete a vector index

`deleteIndex(indexName)`

Deletes an index from this bucket Convenience method that automatically includes the bucket name

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * indexNamestring

Name of the index to delete


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


Delete an index


    1

    const bucket = supabase.storage.vectors.from('embeddings-prod')

    2

    await bucket.deleteIndex('old-index')

* * *

## Retrieve a vector index

`getIndex(indexName)`

Retrieves metadata for a specific index in this bucket Convenience method that automatically includes the bucket name

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * indexNamestring

Name of the index to retrieve


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


Get index metadata


    1

    const bucket = supabase.storage.vectors.from('embeddings-prod')

    2

    const { data } = await bucket.getIndex('documents-openai')

    3

    console.log('Dimension:', data?.index.dimension)

* * *

## List all vector indexes

`listIndexes(options)`

Lists indexes in this bucket Convenience method that automatically includes the bucket name

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * optionsOmit

Listing options (vectorBucketName is automatically set)


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


List indexes


    1

    const bucket = supabase.storage.vectors.from('embeddings-prod')

    2

    const { data } = await bucket.listIndexes({ prefix: 'documents-' })

* * *

## Access a vector index

`VectorBucketScope(indexName)`

Access operations for a specific index within this bucket Returns a scoped client for vector data operations

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * indexNamestring

Name of the index


Accessing an index


    1

    const index = supabase.storage.vectors.from('embeddings-prod').index('documents-openai')

    2

    3

    // Insert vectors

    4

    await index.putVectors({

    5

      vectors: [

    6

        { key: 'doc-1', data: { float32: [...] }, metadata: { title: 'Intro' } }

    7

      ]

    8

    })

    9

    10

    // Query similar vectors

    11

    const { data } = await index.queryVectors({

    12

      queryVector: { float32: [...] },

    13

      topK: 5

    14

    })

* * *

## Delete vectors from index

`deleteVectors(options)`

Deletes vectors by keys from this index Convenience method that automatically includes bucket and index names

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * optionsOmit

Deletion options (bucket and index names automatically set)


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


Delete vectors by keys


    1

    const index = supabase.storage.vectors.from('embeddings-prod').index('documents-openai')

    2

    await index.deleteVectors({

    3

      keys: ['doc-1', 'doc-2', 'doc-3']

    4

    })

* * *

## Retrieve vectors from index

`getVectors(options)`

Retrieves vectors by keys from this index Convenience method that automatically includes bucket and index names

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * optionsOmit

Vector retrieval options (bucket and index names automatically set)


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


Get vectors by keys


    1

    const index = supabase.storage.vectors.from('embeddings-prod').index('documents-openai')

    2

    const { data } = await index.getVectors({

    3

      keys: ['doc-1', 'doc-2'],

    4

      returnMetadata: true

    5

    })

* * *

## List vectors in index

`listVectors(options)`

Lists vectors in this index with pagination Convenience method that automatically includes bucket and index names

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * optionsOmit

Listing options (bucket and index names automatically set)


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


List vectors with pagination


    1

    const index = supabase.storage.vectors.from('embeddings-prod').index('documents-openai')

    2

    const { data } = await index.listVectors({

    3

      maxResults: 500,

    4

      returnMetadata: true

    5

    })

* * *

## Add vectors to index

`putVectors(options)`

Inserts or updates vectors in this index Convenience method that automatically includes bucket and index names

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * optionsOmit

Vector insertion options (bucket and index names automatically set)


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


Insert vectors into an index


    1

    const index = supabase.storage.vectors.from('embeddings-prod').index('documents-openai')

    2

    await index.putVectors({

    3

      vectors: [

    4

        {

    5

          key: 'doc-1',

    6

          data: { float32: [0.1, 0.2, ...] },

    7

          metadata: { title: 'Introduction', page: 1 }

    8

        }

    9

      ]

    10

    })

* * *

## Search vectors in index

`queryVectors(options)`

Queries for similar vectors in this index Convenience method that automatically includes bucket and index names

**Public alpha:** This API is part of a public alpha release and may not be available to your account type.

### Parameters

  * optionsOmit

Query options (bucket and index names automatically set)


### Return Type

Promise<One of the following options>

Details

  * Option 1SuccessResponse

Details

  * Option 2ErrorResponse

Details


Query similar vectors


    1

    const index = supabase.storage.vectors.from('embeddings-prod').index('documents-openai')

    2

    const { data } = await index.queryVectors({

    3

      queryVector: { float32: [0.1, 0.2, ...] },

    4

      topK: 5,

    5

      filter: { category: 'technical' },

    6

      returnDistance: true,

    7

      returnMetadata: true

    8

    })