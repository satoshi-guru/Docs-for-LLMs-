---
title: ""
url: https://supabase.com/docs/guides/platform/migrating-to-supabase/vercel-postgres
---

# 

Platform

#

Migrate from Vercel Postgres to Supabase

##

Migrate your existing Vercel Postgres database to Supabase.

* * *

This guide demonstrates how to migrate your Vercel Postgres database to Supabase to get the most out of Postgres while gaining access to all the features you need to build a project.

## Retrieve your Vercel Postgres database credentials #

  1. Log in to your Vercel Dashboard <https://vercel.com/login>.
  2. Click on the **Storage** tab.
  3. Click on your Postgres Database.
  4. Under the **Quickstart** section, select **psql** then click **Show Secret** to reveal your database password.
  5. Copy the string after `psql ` to the clipboard.


Example:


    1

    psql "postgres://default:xxxxxxxxxxxx@yy-yyyyy-yyyyyy-yyyyyyy.us-west-2.aws.neon.tech:5432/verceldb?sslmode=require"

Copy this part to your clipboard:


    1

    "postgres://default:xxxxxxxxxxxx@yy-yyyyy-yyyyyy-yyyyyyy.us-west-2.aws.neon.tech:5432/verceldb?sslmode=require"

## Set your `OLD_DB_URL` environment variable#

Set the **OLD_DB_URL** environment variable at the command line using your Vercel Postgres Database credentials.

Example:


    1

    export OLD_DB_URL="postgres://default:xxxxxxxxxxxx@yy-yyyyy-yyyyyy-yyyyyyy.us-west-2.aws.neon.tech:5432/verceldb?sslmode=require"

## Retrieve your Supabase connection string #

  1. If you're new to Supabase, [create a project](/dashboard). Make a note of your password, you will need this later. If you forget it, you can [reset it here](/dashboard/project/_/database/settings).

  2. On your project dashboard, click [Connect](/dashboard/project/_?showConnect=true&method=session)

  3. Under the Session pooler, click the **Copy** button to the right of your connection string to copy it to the clipboard.


## Set your `NEW_DB_URL` environment variable#

Set the **NEW_DB_URL** environment variable at the command line using your Supabase connection string. You will need to replace `[YOUR-PASSWORD]` with your actual database password.

Example:


    1

    export NEW_DB_URL="postgresql://postgres.xxxxxxxxxxxxxxxxxxxx:[YOUR-PASSWORD]@aws-0-us-west-1.pooler.supabase.com:5432/postgres"

## Migrate the database#

You will need the [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html) and [psql](https://www.postgresql.org/docs/current/app-psql.html) command line tools, which are included in a full [Postgres installation](https://www.postgresql.org/download).

  1. Export your database to a file in console

Use `pg_dump` with your Postgres credentials to export your database to a file (e.g., `dump.sql`).




    1

    pg_dump "$OLD_DB_URL" \

    2

      --clean \

    3

      --if-exists \

    4

      --quote-all-identifiers \

    5

      --no-owner \

    6

      --no-privileges \

    7

      > dump.sql

  2. Import the database to your Supabase project

Use `psql` to import the Postgres database file to your Supabase project.

         1

         psql -d "$NEW_DB_URL" -f dump.sql


Additional options

  * To only migrate a single database schema, add the `--schema=PATTERN` parameter to your `pg_dump` command.
  * To exclude a schema: `--exclude-schema=PATTERN`.
  * To only migrate a single table: `--table=PATTERN`.
  * To exclude a table: `--exclude-table=PATTERN`.


Run `pg_dump --help` for a full list of options.

  * If you're planning to migrate a database larger than 6 GB, we recommend [upgrading to at least a Large compute add-on](/docs/guides/platform/compute-add-ons). This will ensure you have the necessary resources to handle the migration efficiently.

  * We strongly advise you to pre-provision the disk space you will need for your migration. On paid projects, you can do this by navigating to the [Compute and Disk Settings](/dashboard/project/_/settings/compute-and-disk) page. For more information on disk scaling and disk limits, check out our [disk settings](/docs/guides/platform/compute-and-disk#disk) documentation.


## Enterprise#

[Contact us](https://forms.supabase.com/enterprise) if you need more help migrating your project.

### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/platform/migrating-to-supabase/vercel-postgres so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/platform/migrating-to-supabase/vercel-postgres so I can ask questions about its contents)