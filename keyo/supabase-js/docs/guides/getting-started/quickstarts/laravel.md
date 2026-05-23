---
title: ""
url: https://supabase.com/docs/guides/getting-started/quickstarts/laravel
---

# 

Getting Started

#

Use Supabase with Laravel

##

Learn how to create a PHP Laravel project, connect it to your Supabase Postgres database, and configure user authentication.

* * *

1

### Create a Laravel Project

Make sure your PHP and Composer versions are up to date, then use `composer create-project` to scaffold a new Laravel project.

See the [Laravel docs](https://laravel.com/docs/10.x/installation#creating-a-laravel-project) for more details.

###### Terminal


    1

    composer create-project laravel/laravel example-app

2

### Install the Authentication template

Install [Laravel Breeze](https://laravel.com/docs/10.x/starter-kits#laravel-breeze), a simple implementation of all of Laravel's [authentication features](https://laravel.com/docs/10.x/authentication).

###### Terminal


    1

    composer require laravel/breeze --dev

    2

    php artisan breeze:install

3

### Set up the Postgres connection details

Go to [database.new](https://database.new) and create a new Supabase project. Save your database password securely.

When your project is up and running, navigate to your project dashboard and click on [Connect](/dashboard/project/_?showConnect=true&method=session).

Look for the Session Pooler connection string and copy the string. You will need to replace the Password with your saved database password. You can reset your database password in your [Database Settings](/dashboard/project/_/database/settings) if you do not have it.

If you're in an [IPv6 environment](https://github.com/orgs/supabase/discussions/27034) or have the IPv4 Add-On, you can use the direct connection string instead of Supavisor in Session mode.

###### .env


    1

    DB_CONNECTION=pgsql

    2

    DB_URL=postgres://postgres.xxxx:password@xxxx.pooler.supabase.com:5432/postgres

4

### Change the default schema

By default Laravel uses the `public` schema. We recommend changing this as Supabase exposes the `public` schema as a [data API](/docs/guides/api).

You can change the schema of your Laravel application by modifying the `search_path` variable `app/config/database.php`.

The schema you specify in `search_path` has to exist on Supabase. You can create a new schema from the [Table Editor](/dashboard/project/_/editor).

###### app/config/database.php


    1

    'pgsql' => [

    2

        'driver' => 'pgsql',

    3

        'url' => env('DB_URL'),

    4

        'host' => env('DB_HOST', '127.0.0.1'),

    5

        'port' => env('DB_PORT', '5432'),

    6

        'database' => env('DB_DATABASE', 'laravel'),

    7

        'username' => env('DB_USERNAME', 'root'),

    8

        'password' => env('DB_PASSWORD', ''),

    9

        'charset' => env('DB_CHARSET', 'utf8'),

    10

        'prefix' => '',

    11

        'prefix_indexes' => true,

    12

        'search_path' => 'laravel',

    13

        'sslmode' => 'prefer',

    14

    ],

5

### Run the database migrations

Laravel ships with database migration files that set up the required tables for Laravel Authentication and User Management.

Note: Laravel does not use Supabase Auth but rather implements its own authentication system!

###### Terminal


    1

    php artisan migrate

6

### Start the app

Run the development server. Go to <http://127.0.0.1:8000> in a browser to see your application. You can also navigate to <http://127.0.0.1:8000/register> and <http://127.0.0.1:8000/login> to register and log in users.

###### Terminal


    1

    php artisan serve

### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/getting-started/quickstarts/laravel so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/getting-started/quickstarts/laravel so I can ask questions about its contents)