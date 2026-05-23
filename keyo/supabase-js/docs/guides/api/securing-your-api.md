---
title: ""
url: https://supabase.com/docs/guides/api/securing-your-api
---

# 

REST API

#

Securing your API

* * *

The Data API is designed to work with Postgres' built-in access controls. Two layers work together:

  1. **Grants** determine which Postgres roles (`anon`, `authenticated`, `service_role`) can reach a given table, view, or function over the Data API.
  2. **Row Level Security (RLS) policies** then determine which rows those roles can read or modify from the tables exposed in step 1.
  3. **Both together** grant control _whether_ a role can touch an object. RLS controls _what_ rows they see.


## Grant access explicitly#

A table isn't reachable through the Data API unless you have granted a role privileges on it. Grant the minimum privileges each role needs. For example:


    1

    -- Read-only access for anonymous clients

    2

    grant select on table public.your_table to anon;

    3

    4

    -- Full access for signed-in users (still subject to RLS)

    5

    grant select, insert, update, delete on table public.your_table to authenticated;

    6

    7

    -- Full access for server-side code using the service role

    8

    grant select, insert, update, delete on table public.your_table to service_role;

    9

    10

    -- For functions, grant EXECUTE to the roles that should call them

    11

    grant execute on function public.your_function() to anon, authenticated;

If a required grant is missing, PostgREST returns a `42501` error with a hint that names the exact `GRANT` statement you need:


    1

    {

    2

      "code": "42501",

    3

      "message": "permission denied for table your_table",

    4

      "hint": "Grant the required privileges to the current role with: GRANT SELECT ON public.your_table TO anon;"

    5

    }

See [the Database API 42501 errors troubleshooting guide](/docs/guides/troubleshooting/database-api-42501-errors) for the full troubleshooting flow.

Bundle grants with your RLS setup in the same migration. They belong together: `grant` controls role access, `enable row level security` and policies control row access.

## Default privileges for new tables and functions#

By default on existing projects, tables and functions you create in `public` are automatically granted `SELECT`, `INSERT`, `UPDATE`, `DELETE` (or `EXECUTE` for functions) to `anon`, `authenticated`, and `service_role`. That means a new table is reachable through the Data API the moment it lands, even if you forgot to enable RLS or did not intend to expose it.

Supabase is moving the platform default to **revoke** these automatic grants, so that exposure becomes opt-in, read more about the change in [this changelog entry](https://github.com/orgs/supabase/discussions/45329).

To opt an existing project in today, open the [SQL Editor](/dashboard/project/_/sql/new) and run:


    1

    alter default privileges for role postgres in schema public

    2

      revoke select, insert, update, delete on tables from anon, authenticated, service_role;

    3

    4

    alter default privileges for role postgres in schema public

    5

      revoke execute on functions from anon, authenticated, service_role;

    6

    7

    alter default privileges for role postgres in schema public

    8

      revoke usage, select on sequences from anon, authenticated, service_role;

    9

    10

    alter default privileges for role postgres in schema public

    11

      revoke execute on functions from public;

## Use a dedicated API schema#

If you want an extra boundary around your Data API, lock down the `public` schema and expose a dedicated schema, such as `api`, instead. You can control access with grants in any schema, but this can make the surface easier to reason about: objects in `api` represent your Data API, while internal tables and helper functions stay in schemas that are not exposed. See [Using Custom Schemas](/docs/guides/api/using-custom-schemas) for setup steps.

## Disable the Data API#

If your app never uses Supabase client libraries, REST, or GraphQL data endpoints, turn the Data API off:

  1. Open the [Data API integration overview](/dashboard/project/_/integrations/data_api/overview) in the Dashboard.
  2. Turn **Enable Data API** off.


With the Data API disabled, none of the auto-generated REST endpoints respond, regardless of grants or RLS.

## Add RLS policies#

Enable Row Level Security (RLS) on all tables and views you have exposed via the Data API. You can then write RLS policies to grant users access to specific database rows based on their authentication token.

For functions, RLS does not apply. Instead, control access by granting `EXECUTE` privileges only to the roles that should be able to call the function, and review any `SECURITY DEFINER` functions carefully.

Always enable Row Level Security on tables and views you expose via the Data API to protect your data. For functions, restrict access by granting `EXECUTE` only to appropriate roles.

Any table created through the Supabase Dashboard will have RLS enabled by default. If you created the tables via the SQL editor or via another way, enable RLS like so:

DashboardSQL

  1. Go to the [Authentication > Policies](/dashboard/project/_/auth/policies) page in the Dashboard.
  2. Select **Enable RLS** to enable Row Level Security.


With RLS enabled, you can create Policies that allow or disallow users to access and update data. We provide a detailed guide for creating Row Level Security Policies in our [Authorization documentation](/docs/guides/database/postgres/row-level-security).

Any granted table **without RLS enabled** can be accessed by roles with matching Data API grants (for example, `anon`). Always make sure RLS is enabled, or that you've got other controls in place to avoid unauthorized access to your project's data.

## Enforce additional rules on each request#

Using Row Level Security policies may not always be adequate or sufficient to protect APIs.

Here are some common situations where additional protections are necessary:

  * Enforcing per-IP or per-user rate limits.
  * Checking custom or additional API keys before allowing further access.
  * Rejecting requests after exceeding a quota or requiring payment.
  * Disallowing direct access to certain tables, views, or functions in exposed schemas.


You can build these cases in your application by creating a Postgres function that will read information from the request and perform additional checks, such as counting the number of requests received or checking that an API key is already registered in your database before serving the response.

Define a function like so:


    1

    create function public.check_request()

    2

      returns void

    3

      language plpgsql

    4

      security definer

    5

      as $$

    6

    begin

    7

      -- your logic here

    8

    end;

    9

    $$;

And register it to run on every Data API request using:


    1

    alter role authenticator

    2

      set pgrst.db_pre_request = 'public.check_request';

This configures the `public.check_request` function to run on every Data API request. To have the changes take effect, you should run:


    1

    notify pgrst, 'reload config';

The `pgrst.db_pre_request` configuration only works with the **Data API** (PostgREST). It does not work with Realtime, Storage, or other Supabase products.

If you're using `db_pre_request` to call a function (like `set_information()`) that sets up context or performs checks on every request, and you need similar behavior for other Supabase products, you must call the function directly in your Row Level Security (RLS) policies instead.

**Example:**

If you have a `db_pre_request` function that calls `set_information()` that returns `true` to set up context or perform checks, and you have an RLS policy like:


    1

    create policy "Individuals can view their own todos."

    2

    on todos for select

    3

    using ( (select auth.uid()) = user_id );

To achieve the same behavior with other Supabase products, you need to call the function directly in your RLS policy:


    1

    create policy "Individuals can view their own todos."

    2

    on todos for select

    3

    using ( set_information() AND (select auth.uid()) = user_id );

This ensures the function is called when evaluating RLS policies for all products, not just Data API requests.

**Performance consideration:**

Be aware that calling functions directly in RLS policies can impact database performance, as the function is evaluated for each row when the policy is checked. Consider optimizing your function or using caching strategies if performance becomes an issue.

Inside the function you can perform any additional checks on the request headers or JWT and raise an exception to prevent the request from completing. For example, this exception raises an HTTP 402 Payment Required response with a `hint` and additional `X-Powered-By` header:


    1

    raise sqlstate 'PGRST' using

    2

      message = json_build_object(

    3

        'code',    '123',

    4

        'message', 'Payment Required',

    5

        'details', 'Quota exceeded',

    6

        'hint',    'Upgrade your plan')::text,

    7

      detail = json_build_object(

    8

        'status',  402,

    9

        'headers', json_build_object(

    10

          'X-Powered-By', 'Nerd Rage'))::text;

When raised within the `public.check_request` function, the resulting HTTP response will look like:


    1

    HTTP/1.1 402 Payment Required

    2

    Content-Type: application/json; charset=utf-8

    3

    X-Powered-By: Nerd Rage

    4

    5

    {

    6

      "message": "Payment Required",

    7

      "details": "Quota exceeded",

    8

      "hint": "Upgrade your plan",

    9

      "code": "123"

    10

    }

Use the [JSON operator functions](https://www.postgresql.org/docs/current/functions-json.html) to build rich and dynamic responses from exceptions.

If you use a custom HTTP status code like 419, you can supply the `status_text` key in the `detail` clause of the exception to describe the HTTP status.

If you're using PostgREST version 11 or lower ([find out your PostgREST version](/dashboard/project/_/settings/infrastructure)) a different and less powerful [syntax](https://postgrest.org/en/stable/references/errors.html#raise-errors-with-http-status-codes) needs to be used.

### Accessing request information#

Like with RLS policies, you can access information about the request by using the `current_setting()` Postgres function. Here are some examples on how this works:


    1

    -- To get all the headers sent in the request

    2

    SELECT current_setting('request.headers', true)::json;

    3

    4

    -- To get a single header, you can use JSON arrow operators

    5

    SELECT current_setting('request.headers', true)::json->>'user-agent';

    6

    7

    -- Access Cookies

    8

    SELECT current_setting('request.cookies', true)::json;

`current_setting()`| Example| Description
---|---|---
`request.method`| `GET`, `HEAD`, `POST`, `PUT`, `PATCH`, `DELETE`| Request's method
`request.path`| `table`| Table's path
`request.path`| `view`| View's path
`request.path`| `rpc/function`| Functions's path
`request.headers`| `{ "User-Agent": "...", ... }`| JSON object of the request's headers
`request.cookies`| `{ "cookieA": "...", "cookieB": "..." }`| JSON object of the request's cookies
`request.jwt`| `{ "sub": "a7194ea3-...", ... }`| JSON object of the JWT payload

To access the IP address of the client look up the [X-Forwarded-For header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-For) in the `request.headers` setting. For example:


    1

    SELECT split_part(

    2

      current_setting('request.headers', true)::json->>'x-forwarded-for',

    3

      ',', 1); -- takes the client IP before the first comma (,)

Read more about [PostgREST's pre-request function](https://postgrest.org/en/stable/references/transactions.html#pre-request).

### Examples#

Rate limit per IPUse additional API keys

You can only rate-limit `POST`, `PUT`, `PATCH` and `DELETE` requests. This is because `GET` and `HEAD` requests run in read-only mode, and will be served by [Read Replicas](/docs/guides/platform/read-replicas) which do not support writing to the database.

Outline:

  * A new row is added to a `private.rate_limits` table each time a modifying action is done to the database containing the IP address and the timestamp of the action.
  * If there are over 100 requests from the same IP address in the last 5 minutes, the request is rejected with an HTTP 420 code.


Create the table:


    1

    create table private.rate_limits (

    2

      ip inet,

    3

      request_at timestamp

    4

    );

    5

    6

    -- add an index so that lookups are fast

    7

    create index rate_limits_ip_request_at_idx on private.rate_limits (ip, request_at desc);

The `private` schema is used as it cannot be accessed over the API!

Create the `public.check_request` function:


    1

    create function public.check_request()

    2

      returns void

    3

      language plpgsql

    4

      security definer

    5

      as $$

    6

    declare

    7

      req_method text := current_setting('request.method', true);

    8

      req_ip inet := split_part(

    9

        current_setting('request.headers', true)::json->>'x-forwarded-for',

    10

        ',', 1)::inet;

    11

      count_in_five_mins integer;

    12

    begin

    13

      if req_method = 'GET' or req_method = 'HEAD' or req_method is null then

    14

        -- rate limiting can't be done on GET and HEAD requests

    15

        return;

    16

      end if;

    17

    18

      select

    19

        count(*) into count_in_five_mins

    20

      from private.rate_limits

    21

      where

    22

        ip = req_ip and request_at between now() - interval '5 minutes' and now();

    23

    24

      if count_in_five_mins > 100 then

    25

        raise sqlstate 'PGRST' using

    26

          message = json_build_object(

    27

            'message', 'Rate limit exceeded, try again after a while')::text,

    28

          detail = json_build_object(

    29

            'status',  420,

    30

            'status_text', 'Enhance Your Calm')::text;

    31

      end if;

    32

    33

      insert into private.rate_limits (ip, request_at) values (req_ip, now());

    34

    end;

    35

      $$;

Finally, configure the `public.check_request()` function to run on every Data API request:


    1

    alter role authenticator

    2

      set pgrst.db_pre_request = 'public.check_request';

    3

    4

    notify pgrst, 'reload config';

The `pgrst.db_pre_request` configuration only works with the **Data API** (PostgREST). It does not work with Realtime, Storage, or other Supabase products.

If you're using `db_pre_request` to call a function (like `set_information()`) that sets up context or performs checks on every request, and you need similar behavior for other Supabase products, you must call the function directly in your Row Level Security (RLS) policies instead.

**Example:**

If you have a `db_pre_request` function that calls `set_information()` that returns `true` to set up context or perform checks, and you have an RLS policy like:


    1

    create policy "Individuals can view their own todos."

    2

    on todos for select

    3

    using ( (select auth.uid()) = user_id );

To achieve the same behavior with other Supabase products, you need to call the function directly in your RLS policy:


    1

    create policy "Individuals can view their own todos."

    2

    on todos for select

    3

    using ( set_information() AND (select auth.uid()) = user_id );

This ensures the function is called when evaluating RLS policies for all products, not just Data API requests.

**Performance consideration:**

Be aware that calling functions directly in RLS policies can impact database performance, as the function is evaluated for each row when the policy is checked. Consider optimizing your function or using caching strategies if performance becomes an issue.

To clear old entries in the `private.rate_limits` table, set up a [pg_cron](/docs/guides/database/extensions/pg_cron) job to clean them up.

### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/api/securing-your-api so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/api/securing-your-api so I can ask questions about its contents)