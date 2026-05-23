---
title: ""
url: https://supabase.com/docs/guides/cli/getting-started
---

# 

Local Development

#

Supabase CLI

##

Develop locally, deploy to the Supabase Platform, and set up CI/CD workflows

* * *

The Supabase CLI enables you to run the entire Supabase stack locally, on your machine or in a CI environment. With just two commands, you can set up and start a new local project:

  1. `supabase init` to create a new local project
  2. `supabase start` to launch the Supabase services


## Installing the Supabase CLI#

macOSWindowsLinuxnodejs

Run the CLI by prefixing each command with `npx` or `bunx`:


    1

    npx supabase --help

The Supabase CLI requires **Node.js 20 or later** when run via `npx` or `npm`. Older Node.js versions, such as 16, are not supported and fail to start the CLI.

Installing the Supabase CLI globally using `npm install -g supabase` is **not supported**.

For global usage, install the CLI via Homebrew, Scoop, or the standalone binary.

Alternatively, you can run the CLI using `npx supabase` or install it locally as a dev dependency.

You can also install the CLI as dev dependency via [npm](https://www.npmjs.com/package/supabase):


    1

    npm install supabase --save-dev

Global installation using `npm install -g supabase` is not supported. For global CLI usage, install via [Homebrew](/docs/guides/local-development/cli/getting-started?queryGroups=platform&platform=macos), [Scoop](/docs/guides/local-development/cli/getting-started?queryGroups=platform&platform=windows), or the [standalone binary](/docs/guides/local-development/cli/getting-started?queryGroups=platform&platform=linux).

## Beta channel#

Pre-release CLI builds ship from the development branch (`X.Y.Z-beta.N` versions). Use the npm `beta` dist-tag, or install `supabase-beta` via Homebrew / Scoop (separate packages from stable).

macOSWindowsLinuxnodejs

Install as a dev dependency:


    1

    npm install supabase@beta --save-dev

Or run without installing:


    1

    npx supabase@beta --help

## Updating the Supabase CLI#

When a new [version](https://github.com/supabase/cli/releases) is released, you can update the CLI using the same methods.

macOSWindowsLinuxnodejs

If you have installed the CLI as dev dependency via [npm](https://www.npmjs.com/package/supabase), you can update it with:


    1

    npm update supabase --save-dev

Beta channel (`supabase@beta`):


    1

    npm update supabase@beta --save-dev

If you have any Supabase containers running locally, stop them and delete their data volumes before proceeding with the upgrade. This ensures that Supabase managed services can apply new migrations on a clean state of the local database.

##### Backup and stop running containers

Remember to save any local schema and data changes before stopping because the `--no-backup` flag will delete them.


    1

    supabase db diff -f my_schema

    2

    supabase db dump --local --data-only > supabase/seed.sql

    3

    supabase stop --no-backup

## Running Supabase locally#

The Supabase CLI uses Docker containers to manage the local development stack. Follow the official guide to install and configure [Docker Desktop](https://docs.docker.com/desktop):

macOSWindows

Alternately, you can use a different container tool that offers Docker compatible APIs.

  * [Rancher Desktop](https://rancherdesktop.io/) (macOS, Windows, Linux)
  * [Podman](https://podman.io/) (macOS, Windows, Linux)
  * [OrbStack](https://orbstack.dev/) (macOS)
  * [colima](https://github.com/abiosoft/colima) (macOS)


Inside the folder where you want to create your project, run:


    1

    supabase init

This will create a new `supabase` folder. It's safe to commit this folder to your version control system.

Now, to start the Supabase stack, run:


    1

    supabase start

This takes time on your first run because the CLI needs to download the Docker images to your local machine. The CLI includes the entire Supabase toolset, and a few additional images that are useful for local development (like a local SMTP server and a database diff tool).

## Access your project's services#

Once all of the Supabase services are running, you'll see output containing your local Supabase credentials. It should look like this, with urls and keys that you'll use in your local project:


    1

    Started supabase local development setup.

    2

    3

    ╭──────────────────────────────────────╮

    4

    │ 🔧 Development Tools                 │

    5

    ├─────────┬────────────────────────────┤

    6

    │ Studio  │ http://127.0.0.1:54323     │

    7

    │ Mailpit │ http://127.0.0.1:54324     │

    8

    │ MCP     │ http://127.0.0.1:54321/mcp │

    9

    ╰─────────┴────────────────────────────╯

    10

    11

    ╭──────────────────────────────────────────────────────╮

    12

    │ 🌐 APIs                                              │

    13

    ├────────────────┬─────────────────────────────────────┤

    14

    │ Project URL    │ http://127.0.0.1:54321              │

    15

    │ REST           │ http://127.0.0.1:54321/rest/v1      │

    16

    │ GraphQL        │ http://127.0.0.1:54321/graphql/v1   │

    17

    │ Edge Functions │ http://127.0.0.1:54321/functions/v1 │

    18

    ╰────────────────┴─────────────────────────────────────╯

    19

    20

    ╭───────────────────────────────────────────────────────────────╮

    21

    │ ⛁ Database                                                    │

    22

    ├─────┬─────────────────────────────────────────────────────────┤

    23

    │ URL │ postgresql://postgres:postgres@127.0.0.1:54322/postgres │

    24

    ╰─────┴─────────────────────────────────────────────────────────╯

    25

    26

    ╭──────────────────────────────────────────────────────────────╮

    27

    │ 🔑 Authentication Keys                                       │

    28

    ├─────────────┬────────────────────────────────────────────────┤

    29

    │ Publishable │ sb_publishable_...                             │

    30

    │ Secret      │ sb_secret_...                                  │

    31

    ╰─────────────┴────────────────────────────────────────────────╯

StudioPostgresAPI GatewayAnalytics


    1

    # Default URL:

    2

    http://localhost:54323

The local development environment includes Supabase Studio, a graphical interface for working with your database.

## Stopping local services#

When you are finished working on your Supabase project, you can stop the stack (without resetting your local database):


    1

    supabase stop

## Telemetry#

The Supabase CLI collects telemetry data about general usage. Participating in this program is optional, and you can opt out at any time.

### How to opt out#

You can disable telemetry by running:


    1

    supabase telemetry disable

You can check the current status and re-enable with:


    1

    supabase telemetry status

    2

    supabase telemetry enable

You can also opt out using the `SUPABASE_TELEMETRY_DISABLED=1` environment variable. The broader `DO_NOT_TRACK=1` convention is also respected.

## Learn more#

  * [CLI configuration](/docs/guides/local-development/cli/config)
  * [CLI reference](/docs/reference/cli)


### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/local-development/cli/getting-started so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/local-development/cli/getting-started so I can ask questions about its contents)