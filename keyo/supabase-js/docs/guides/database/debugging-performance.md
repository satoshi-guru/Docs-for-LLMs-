---
title: ""
url: https://supabase.com/docs/guides/database/debugging-performance
---

# 

Database

#

Debugging performance issues

##

Debug slow-running queries using the Postgres execution planner.

* * *

`explain()` is a method that provides the Postgres `EXPLAIN` execution plan of a query. It is a powerful tool for debugging slow queries and understanding how Postgres will execute a given query. This feature is applicable to any query, including those made through `rpc()` or write operations.

## Enabling `explain()`#

`explain()` is disabled by default to protect sensitive information about your database structure and operations. We recommend using `explain()` in a non-production environment. Run the following SQL to enable `explain()`:


    1

    -- enable explain

    2

    alter role authenticator

    3

    set pgrst.db_plan_enabled to 'true';

    4

    5

    -- reload the config

    6

    notify pgrst, 'reload config';

## Using `explain()`#

To get the execution plan of a query, you can chain the `explain()` method to a Supabase query:


    1

    const { data, error } = await supabase

    2

      .from('instruments')

    3

      .select()

    4

      .explain()

### Example data#

To illustrate, consider the following setup of a `instruments` table:


    1

    create table instruments (

    2

      id int8 primary key,

    3

      name text

    4

    );

    5

    6

    insert into books

    7

      (id, name)

    8

    values

    9

      (1, 'violin'),

    10

      (2, 'viola'),

    11

      (3, 'cello');

### Expected response#

The response would typically look like this:


    1

    Aggregate  (cost=33.34..33.36 rows=1 width=112)

    2

      ->  Limit  (cost=0.00..18.33 rows=1000 width=40)

    3

            ->  Seq Scan on instruments  (cost=0.00..22.00 rows=1200 width=40)

By default, the execution plan is returned in TEXT format. However, you can also retrieve it as JSON by specifying the `format` parameter.

## Production use with pre-request protection#

If you need to enable `explain()` in a production environment, ensure you protect your database by restricting access to the `explain()` feature. You can do so by using a pre-request function that filters requests based on the IP address:


    1

    create or replace function filter_plan_requests()

    2

    returns void as $$

    3

    declare

    4

      headers   json := current_setting('request.headers', true)::json;

    5

      client_ip text := coalesce(headers->>'cf-connecting-ip', '');

    6

      accept    text := coalesce(headers->>'accept', '');

    7

      your_ip   text := '123.123.123.123'; -- replace this with your IP

    8

    begin

    9

      if accept like 'application/vnd.pgrst.plan%' and client_ip != your_ip then

    10

        raise insufficient_privilege using

    11

          message = 'Not allowed to use application/vnd.pgrst.plan';

    12

      end if;

    13

    end; $$ language plpgsql;

    14

    alter role authenticator set pgrst.db_pre_request to 'filter_plan_requests';

    15

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

Replace `'123.123.123.123'` with your actual IP address.

## Disabling explain#

To disable the `explain()` method after use, execute the following SQL commands:


    1

    -- disable explain

    2

    alter role authenticator

    3

    set pgrst.db_plan_enabled to 'false';

    4

    5

    -- if you used the above pre-request

    6

    alter role authenticator

    7

    set pgrst.db_pre_request to '';

    8

    9

    -- reload the config

    10

    notify pgrst, 'reload config';

### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/database/debugging-performance so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/database/debugging-performance so I can ask questions about its contents)