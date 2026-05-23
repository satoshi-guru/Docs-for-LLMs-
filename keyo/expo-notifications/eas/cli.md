---
title: "EAS CLI reference"
url: https://docs.expo.dev/eas/cli
---

# EAS CLI reference

# EAS CLI reference

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/eas/cli.mdx)

Copy page

EAS CLI is a command-line tool that allows you to interact with Expo Application Services (EAS) from your terminal.

CLI version:

19.0.5

CLI version 19.0.5

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/eas/cli.mdx)

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

You can use EAS Command-Line Interface (CLI) to build, update, submit, deploy or use workflows in your Expo and React Native project from a terminal window.

## Installation

You need to install the EAS CLI globally on your machine. You do this by running the following command:

Terminalnpmyarnpnpmbun

Copy

`- ``npm install --global eas-cli`

Alternatively, you can use CLI tools provided by your package manager to run EAS CLI commands:

Terminalnpmyarnpnpmbun

Copy

`- ``npx eas-cli@latest`

## Commands

Use the EAS CLI by running one of the commands documented on this page, optionally followed by any flags or arguments. Flags customize the behavior of a command, and arguments are specific to the command.

### `eas account:login`

Log in with your Expo account.

Usage

Terminal

Copy

`- ``eas account:login [-s] [-b]`

Flags

  * `-b, --[no-]browser` Log in with your browser (default; use `--no-browser` for CLI-based login).
  * `-s, --sso` Log in with SSO.


Alias

Terminal

Copy

`- ``eas login`

### `eas account:logout`

Log out.

Usage

Terminal

Copy

`- ``eas account:logout`

Alias

Terminal

Copy

`- ``eas logout`

### `eas account:usage [ACCOUNT_NAME]`

View account usage and billing for the current cycle.

Usage

Terminal

Copy

`- ``eas account:usage [ACCOUNT_NAME] [--json] [--non-interactive]`

Argument

  * `[ACCOUNT_NAME]` Account name to view usage for. If not provided, the account will be selected interactively (or defaults to the only account if there is just one).


Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas account:view`

Show the username you are logged in as.

Usage

Terminal

Copy

`- ``eas account:view`

Alias

Terminal

Copy

`- ``eas whoami`

### `eas analytics [STATUS]`

Display or change analytics settings.

Usage

Terminal

Copy

`- ``eas analytics [STATUS]`

### `eas autocomplete [SHELL]`

Display autocomplete installation instructions.

Usage

Terminal

Copy

`- ``eas autocomplete [SHELL] [-r]`

Argument

  * `[SHELL]` (zsh|bash|powershell) Shell type.


Flag

  * `-r, --refresh-cache` Refresh cache (ignores displaying instructions).


Examples

Terminal

`- ``eas autocomplete`




`- ``eas autocomplete bash`




`- ``eas autocomplete zsh`




`- ``eas autocomplete powershell`




`- ``eas autocomplete --refresh-cache`

### `eas branch:create [NAME]`

Create a branch.

Usage

Terminal

Copy

`- ``eas branch:create [NAME] [--json] [--non-interactive]`

Argument

  * `[NAME]` Name of the branch to create.


Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas branch:delete [NAME]`

Delete a branch.

Usage

Terminal

Copy

`- ``eas branch:delete [NAME] [--json] [--non-interactive]`

Argument

  * `[NAME]` Name of the branch to delete.


Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas branch:list`

List all branches.

Usage

Terminal

Copy

`- ``eas branch:list [--offset <value>] [--limit <value>] [--json] [--non-interactive]`

Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--limit=<value>` The number of items to fetch each query. Defaults to 50 and is capped at 100.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--offset=<value>` Start queries from specified index. Use for paginating results. Defaults to 0.


### `eas branch:rename`

Rename a branch.

Usage

Terminal

Copy

`- ``eas branch:rename [--from <value>] [--to <value>] [--json] [--non-interactive]`

Flags

  * `--from=<value>` Current name of the branch.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--to=<value>` New name of the branch.


### `eas branch:view [NAME]`

View a branch.

Usage

Terminal

Copy

`- ``eas branch:view [NAME] [--offset <value>] [--limit <value>] [--json] [--non-interactive]`

Argument

  * `[NAME]` Name of the branch to view.


Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--limit=<value>` The number of items to fetch each query. Defaults to 25 and is capped at 50.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--offset=<value>` Start queries from specified index. Use for paginating results. Defaults to 0.


### `eas build`

Start a build.

Usage

Terminal

`- ``eas build [-p android|ios|all] [-e PROFILE_NAME] [--local] [--output <value>] [--wait] [--clear-cache]`

`[-s | --auto-submit-with-profile PROFILE_NAME] [--what-to-test <value>] [-m <value>] [--build-logger-level``trace|debug|info|warn|error|fatal] [--freeze-credentials] [--verbose-logs] [--json] [--non-interactive]`

Flags

  * `-e, --profile=PROFILE_NAME` Name of the build profile from eas.json. Defaults to "production" if defined in eas.json.
  * `-m, --message=<value>` A short message describing the build.
  * `-p, --platform=<option>` <options: android|ios|all>.
  * `-s, --auto-submit` Submit on build complete using the submit profile with the same name as the build profile.
  * `--auto-submit-with-profile=PROFILE_NAME` Submit on build complete using the submit profile with provided name.
  * `--build-logger-level=<option>` The level of logs to output during the build process. Defaults to "info". <options: trace|debug|info|warn|error|fatal>.
  * `--clear-cache` Clear cache before the build.
  * `--freeze-credentials` Prevent the build from updating credentials in non-interactive mode.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--local` Run build locally [experimental].
  * `--non-interactive` Run the command in non-interactive mode.
  * `--output=<value>` Output path for local build.
  * `--verbose-logs` Use verbose logs for the build process.
  * `--[no-]wait` Wait for build(s) to complete.
  * `--what-to-test=<value>` Specify the "What to Test" information for the build in TestFlight (iOS-only). To be used with the `auto-submit` flag.


### `eas build:cancel [BUILD_ID]`

Cancel a build.

Usage

Terminal

Copy

`- ``eas build:cancel [BUILD_ID] [--non-interactive] [-p android|ios|all] [-e PROFILE_NAME]`

Flags

  * `-e, --profile=PROFILE_NAME` Filter builds by build profile if build ID is not provided.
  * `-p, --platform=<option>` Filter builds by the platform if build ID is not provided <options: android|ios|all>.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas build:configure`

Configure the project to support EAS Build.

Usage

Terminal

Copy

`- ``eas build:configure [-p android|ios|all]`

Flag

  * `-p, --platform=<option>` Platform to configure <options: android|ios|all>.


### `eas build:delete [BUILD_ID]`

Delete a build.

Usage

Terminal

Copy

`- ``eas build:delete [BUILD_ID] [--non-interactive] [-p android|ios|all] [-e PROFILE_NAME]`

Flags

  * `-e, --profile=PROFILE_NAME` Filter builds by build profile if build ID is not provided.
  * `-p, --platform=<option>` Filter builds by the platform if build ID is not provided <options: android|ios|all>.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas build:dev`

Run dev client simulator/emulator build with matching fingerprint or create a new one.

Usage

Terminal

`- ``eas build:dev [-p ios|android] [-e PROFILE_NAME] [--skip-build-if-not-found] [--skip-bundler] [--simulator`

`<value>]`

Flags

  * `-e, --profile=PROFILE_NAME` Name of the build profile from eas.json. It must be a profile allowing to create emulator/simulator internal distribution dev client builds. The "development-simulator" build profile will be selected by default.
  * `-p, --platform=<option>` <options: ios|android>.
  * `--simulator=<value>` IOS simulator name or UDID to install and run the development build on. If no value is provided, you will be prompted to select a simulator.
  * `--skip-build-if-not-found` Skip build if no successful build with matching fingerprint is found.
  * `--skip-bundler` Install and run the development build without starting the bundler server.


### `eas build:download`

Download a simulator/emulator build by build ID or fingerprint hash.

Usage

Terminal

`- ``eas build:download [--build-id <value> | --fingerprint <value> | -p ios|android | --dev-client]`

`[--all-artifacts] [--json] [--non-interactive]`

Flags

  * `-p, --platform=<option>` <options: ios|android>.
  * `--all-artifacts` Download all available build artifacts (build artifacts archive, Xcode logs, etc.) in addition to the application archive. Without this flag, only the application archive is downloaded and the command errors if it is missing.
  * `--build-id=<value>` ID of the build to download. Mutually exclusive with `--fingerprint`, `--platform`, and `--dev-client`; the platform is derived from the build itself.
  * `--[no-]dev-client` Filter only dev-client builds.
  * `--fingerprint=<value>` Fingerprint hash of the build to download.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas build:inspect`

Inspect the state of the project at specific build stages, useful for troubleshooting.

Usage

Terminal

`- ``eas build:inspect -p android|ios -s archive|pre-build|post-build -o OUTPUT_DIRECTORY [-e PROFILE_NAME]`

`[--force] [-v]`

Flags

  * `-e, --profile=PROFILE_NAME` Name of the build profile from eas.json. Defaults to "production" if defined in eas.json.
  * `-o, --output=OUTPUT_DIRECTORY` (required) Output directory.
  * `-p, --platform=<option>` (required) <options: android|ios>.
  * `-s, --stage=<option>` (required) Stage of the build you want to inspect.
    * `archive` Builds the project archive that would be uploaded to EAS when building.
    * `pre-build` Prepares the project to be built with Gradle/Xcode. Does not run the native build.
    * `post-build` Builds the native project and leaves the output directory for inspection <options: archive|pre-build|post-build>.
  * `-v, --verbose`
  * `--force` Delete OUTPUT_DIRECTORY if it already exists.


### `eas build:list`

List all builds for your project.

Usage

Terminal

`- ``eas build:list [-p android|ios|all] [--status`

`new|in-queue|in-progress|pending-cancel|errored|finished|canceled] [--distribution store|internal|simulator]``[--channel <value>] [--app-version <value>] [--app-build-version <value>] [--sdk-version <value>] [--runtime-version``<value>] [--app-identifier <value>] [-e <value>] [--git-commit-hash <value>] [--fingerprint-hash <value>] [--offset``<value>] [--limit <value>] [--json] [--non-interactive] [--simulator]`

Flags

  * `-e, --build-profile=<value>` Filter only builds created with the specified build profile.
  * `-p, --platform=<option>` <options: android|ios|all>.
  * `--app-build-version=<value>` Filter only builds created with the specified app build version.
  * `--app-identifier=<value>` Filter only builds created with the specified app identifier.
  * `--app-version=<value>` Filter only builds created with the specified main app version.
  * `--channel=<value>`
  * `--distribution=<option>` Filter only builds with the specified distribution type <options: store|internal|simulator>.
  * `--fingerprint-hash=<value>` Filter only builds with the specified fingerprint hash.
  * `--git-commit-hash=<value>` Filter only builds created with the specified git commit hash.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--limit=<value>` The number of items to fetch each query. Defaults to 10 and is capped at 50.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--offset=<value>` Start queries from specified index. Use for paginating results. Defaults to 0.
  * `--runtime-version=<value>` Filter only builds created with the specified runtime version.
  * `--sdk-version=<value>` Filter only builds created with the specified Expo SDK version.
  * `--simulator` Filter only iOS simulator builds. Can only be used with `--platform` flag set to "ios".
  * `--status=<option>` Filter only builds with the specified status <options: new|in-queue|in-progress|pending-cancel|errored|finished|canceled>.


### `eas build:resign`

Re-sign a build archive.

Usage

Terminal

`- ``eas build:resign [-p android|ios] [-e PROFILE_NAME] [--source-profile PROFILE_NAME] [--wait] [--id <value>]`

`[--offset <value>] [--limit <value>] [--json] [--non-interactive]`

Flags

  * `-e, --target-profile=PROFILE_NAME` Name of the target build profile from eas.json. Credentials and environment variables from this profile will be used when re-signing. Defaults to "production" if defined in eas.json.
  * `-p, --platform=<option>` <options: android|ios>.
  * `--id=<value>` ID of the build to re-sign.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--limit=<value>` The number of items to fetch each query. Defaults to 50 and is capped at 100.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--offset=<value>` Start queries from specified index. Use for paginating results. Defaults to 0.
  * `--source-profile=PROFILE_NAME` Name of the source build profile from eas.json. Used to filter builds eligible for re-signing.
  * `--[no-]wait` Wait for build(s) to complete.


### `eas build:run`

Run simulator/emulator builds from eas-cli.

Usage

Terminal

`- ``eas build:run [--latest | --id <value> | --path <value> | --url <value>] [-p android|ios] [-e PROFILE_NAME]`

`[--simulator <value>] [--offset <value>] [--limit <value>]`

Flags

  * `-e, --profile=PROFILE_NAME` Name of the build profile used to create the build to run. When specified, only builds created with the specified build profile will be queried.
  * `-p, --platform=<option>` <options: android|ios>.
  * `--id=<value>` ID of the simulator/emulator build to run.
  * `--latest` Run the latest simulator/emulator build for specified platform.
  * `--limit=<value>` The number of items to fetch each query. Defaults to 50 and is capped at 100.
  * `--offset=<value>` Start queries from specified index. Use for paginating results. Defaults to 0.
  * `--path=<value>` Path to the simulator/emulator build archive or app.
  * `--simulator=<value>` IOS simulator name or UDID to install and run the build on. If no value is provided, you will be prompted to select a simulator.
  * `--url=<value>` Simulator/Emulator build archive url.


### `eas build:submit`

Submit app binary to App Store and/or Play Store.

Usage

Terminal

`- ``eas build:submit [-p android|ios|all] [-e <value>] [--latest | --id <value> | --path <value> | --url <value>]`

`[--what-to-test <value>] [--verbose] [--wait] [--verbose-fastlane] [-g <value>...] [--non-interactive]`

Flags

  * `-e, --profile=<value>` Name of the submit profile from eas.json. Defaults to "production" if defined in eas.json.
  * `-g, --groups=<value>...` Internal TestFlight testing groups to add the build to (iOS only). Learn more: <https://developer.apple.com/help/app-store-connect/test-a-beta-version/add-internal-testers>.
  * `-p, --platform=<option>` <options: android|ios|all>.
  * `--id=<value>` ID of the build to submit.
  * `--latest` Submit the latest build for specified platform.
  * `--non-interactive` Run command in non-interactive mode.
  * `--path=<value>` Path to the .apk/.aab/.ipa file.
  * `--url=<value>` App archive url.
  * `--verbose` Always print logs from EAS Submit.
  * `--verbose-fastlane` Enable verbose logging for the submission process.
  * `--[no-]wait` Wait for submission to complete.
  * `--what-to-test=<value>` Sets the "What to test" information in TestFlight (iOS only).


Alias

Terminal

Copy

`- ``eas build:submit`

### `eas build:version:get`

Get the latest version from EAS servers.

Usage

Terminal

Copy

`- ``eas build:version:get [-p android|ios|all] [-e PROFILE_NAME] [--json] [--non-interactive]`

Flags

  * `-e, --profile=PROFILE_NAME` Name of the build profile from eas.json. Defaults to "production" if defined in eas.json.
  * `-p, --platform=<option>` <options: android|ios|all>.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas build:version:set`

Update version of an app.

Usage

Terminal

Copy

`- ``eas build:version:set [-p android|ios] [-e PROFILE_NAME]`

Flags

  * `-e, --profile=PROFILE_NAME` Name of the build profile from eas.json. Defaults to "production" if defined in eas.json.
  * `-p, --platform=<option>` <options: android|ios>.


### `eas build:version:sync`

Update a version in native code with a value stored on EAS servers.

Usage

Terminal

Copy

`- ``eas build:version:sync [-p android|ios|all] [-e PROFILE_NAME]`

Flags

  * `-e, --profile=PROFILE_NAME` Name of the build profile from eas.json. Defaults to "production" if defined in eas.json.
  * `-p, --platform=<option>` <options: android|ios|all>.


### `eas build:view [BUILD_ID]`

View a build for your project.

Usage

Terminal

Copy

`- ``eas build:view [BUILD_ID] [--json]`

Flag

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`.


### `eas channel:create [NAME]`

Create a channel.

Usage

Terminal

Copy

`- ``eas channel:create [NAME] [--json] [--non-interactive]`

Argument

  * `[NAME]` Name of the channel to create.


Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas channel:delete [NAME]`

Delete a channel.

Usage

Terminal

Copy

`- ``eas channel:delete [NAME] [--json] [--non-interactive]`

Argument

  * `[NAME]` Name of the channel to delete.


Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas channel:edit [NAME]`

Point a channel at a new branch.

Usage

Terminal

Copy

`- ``eas channel:edit [NAME] [--branch <value>] [--json] [--non-interactive]`

Argument

  * `[NAME]` Name of the channel to edit.


Flags

  * `--branch=<value>` Name of the branch to point to.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas channel:insights`

Display adoption, crash, and unique-user insights for a channel + runtime version.

Usage

Terminal

`- ``eas channel:insights --channel <value> --runtime-version <value> [--days <value> | --start <value> | --end`

`<value>] [--json] [--non-interactive]`

Flags

  * `--channel=<value>` (required) Name of the channel.
  * `--days=<value>` Show insights from the last N days (default 7, mutually exclusive with `--start`/`--end`).
  * `--end=<value>` End of insights time range (ISO date).
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--runtime-version=<value>` (required) Runtime version to query insights for.
  * `--start=<value>` Start of insights time range (ISO date).


### `eas channel:list`

List all channels.

Usage

Terminal

Copy

`- ``eas channel:list [--offset <value>] [--limit <value>] [--json] [--non-interactive]`

Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--limit=<value>` The number of items to fetch each query. Defaults to 10 and is capped at 25.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--offset=<value>` Start queries from specified index. Use for paginating results. Defaults to 0.


### `eas channel:pause [NAME]`

Pause a channel to stop it from sending updates.

Usage

Terminal

Copy

`- ``eas channel:pause [NAME] [--branch <value>] [--json] [--non-interactive]`

Argument

  * `[NAME]` Name of the channel to edit.


Flags

  * `--branch=<value>` Name of the branch to point to.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas channel:resume [NAME]`

Resume a channel to start sending updates.

Usage

Terminal

Copy

`- ``eas channel:resume [NAME] [--branch <value>] [--json] [--non-interactive]`

Argument

  * `[NAME]` Name of the channel to edit.


Flags

  * `--branch=<value>` Name of the branch to point to.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas channel:rollout [CHANNEL]`

Roll a new branch out on a channel incrementally.

Usage

Terminal

`- ``eas channel:rollout [CHANNEL] [--action create|edit|end|view] [--percent <value>] [--outcome`

`republish-and-revert|revert] [--branch <value>] [--runtime-version <value>] [--private-key-path <value>] [--json]``[--non-interactive]`

Argument

  * `[CHANNEL]` Channel on which the rollout should be done.


Flags

  * `--action=<option>` Rollout action to perform <options: create|edit|end|view>.
  * `--branch=<value>` Branch to roll out. Use with `--action=create`.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--outcome=<option>` End outcome of rollout. Use with `--action=end` <options: republish-and-revert|revert>.
  * `--percent=<value>` Percent of users to send to the new branch. Use with `--action=edit` or `--action=create`.
  * `--private-key-path=<value>` File containing the PEM-encoded private key corresponding to the certificate in expo-updates' configuration. Defaults to a file named "private-key.pem" in the certificate's directory. Only relevant if you are using code signing: <https://docs.expo.dev/eas-update/code-signing/>.
  * `--runtime-version=<value>` Runtime version to target. Use with `--action=create`.


### `eas channel:view [NAME]`

View a channel.

Usage

Terminal

Copy

`- ``eas channel:view [NAME] [--json] [--non-interactive] [--offset <value>] [--limit <value>]`

Argument

  * `[NAME]` Name of the channel to view.


Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--limit=<value>` The number of items to fetch each query. Defaults to 50 and is capped at 100.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--offset=<value>` Start queries from specified index. Use for paginating results. Defaults to 0.


### `eas config`

Display project configuration (app.json \+ eas.json).

Usage

Terminal

Copy

`- ``eas config [-p android|ios] [-e PROFILE_NAME] [--json] [--non-interactive]`

Flags

  * `-e, --profile=PROFILE_NAME` Name of the build profile from eas.json. Defaults to "production" if defined in eas.json.
  * `-p, --platform=<option>` <options: android|ios>.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas credentials`

Manage credentials.

Usage

Terminal

Copy

`- ``eas credentials [-p android|ios]`

Flag

  * `-p, --platform=<option>` <options: android|ios>.


### `eas credentials:configure-build`

Set up credentials for building your project.

Usage

Terminal

Copy

`- ``eas credentials:configure-build [-p android|ios] [-e PROFILE_NAME]`

Flags

  * `-e, --profile=PROFILE_NAME` The name of the build profile in eas.json.
  * `-p, --platform=<option>` <options: android|ios>.


### `eas deploy [options]`

Deploy your Expo Router web build and API Routes.

Usage

Terminal

`- ``eas deploy [options]`

`- ``eas deploy --prod`

Flags

  * `--alias=name` Custom alias to assign to the new deployment.
  * `--dry-run` Outputs a tarball of the new deployment instead of uploading it.
  * `--environment=<value>` Environment variable's environment, for example, 'production', 'preview', 'development'.
  * `--export-dir=dir` [default: dist] Directory where the Expo project was exported.
  * `--id=xyz123` Custom unique identifier for the new deployment.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--prod` Create a new production deployment.
  * `--[no-]source-maps` Include source maps in the deployment.


Alias

Terminal

Copy

`- ``eas worker:deploy`

### `eas deploy:alias`

Assign deployment aliases.

Usage

Terminal

Copy

`- ``eas deploy:alias [--prod] [--alias name] [--id xyz123] [--json] [--non-interactive]`

Flags

  * `--alias=name` Custom alias to assign to the existing deployment.
  * `--id=xyz123` Unique identifier of an existing deployment.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--prod` Promote an existing deployment to production.


Aliases

Terminal

`- ``eas worker:alias`

`- ``eas deploy:promote`

### `eas deploy:alias:delete [ALIAS_NAME]`

Delete deployment aliases.

Usage

Terminal

Copy

`- ``eas deploy:alias:delete [ALIAS_NAME] [--json] [--non-interactive]`

Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


Alias

Terminal

Copy

`- ``eas worker:alias:delete`

### `eas deploy:delete [DEPLOYMENT_ID]`

Delete a deployment.

Usage

Terminal

Copy

`- ``eas deploy:delete [DEPLOYMENT_ID] [--json] [--non-interactive]`

Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


Alias

Terminal

Copy

`- ``eas worker:delete`

### `eas deploy:promote`

Assign deployment aliases.

Usage

Terminal

Copy

`- ``eas deploy:promote [--prod] [--alias name] [--id xyz123] [--json] [--non-interactive]`

Flags

  * `--alias=name` Custom alias to assign to the existing deployment.
  * `--id=xyz123` Unique identifier of an existing deployment.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--prod` Promote an existing deployment to production.


Aliases

Terminal

`- ``eas worker:alias`

`- ``eas deploy:promote`

### `eas device:create`

Register new Apple Devices to use for internal distribution.

Usage

Terminal

Copy

`- ``eas device:create`

### `eas device:delete`

Remove a registered device from your account.

Usage

Terminal

Copy

`- ``eas device:delete [--apple-team-id <value>] [--udid <value>] [--json] [--non-interactive]`

Flags

  * `--apple-team-id=<value>` The Apple team ID on which to find the device.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--udid=<value>` The Apple device ID to disable.


### `eas device:list`

List all registered devices for your account.

Usage

Terminal

Copy

`- ``eas device:list [--apple-team-id <value>] [--offset <value>] [--limit <value>] [--json] [--non-interactive]`

Flags

  * `--apple-team-id=<value>`
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--limit=<value>` The number of items to fetch each query. Defaults to 50 and is capped at 100.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--offset=<value>` Start queries from specified index. Use for paginating results. Defaults to 0.


### `eas device:rename`

Rename a registered device.

Usage

Terminal

Copy

`- ``eas device:rename [--apple-team-id <value>] [--udid <value>] [--name <value>] [--json] [--non-interactive]`

Flags

  * `--apple-team-id=<value>` The Apple team ID on which to find the device.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--name=<value>` The new name for the device.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--udid=<value>` The Apple device ID to rename.


### `eas device:view [UDID]`

View a device for your project.

Usage

Terminal

Copy

`- ``eas device:view [UDID]`

### `eas diagnostics`

Display environment info.

Usage

Terminal

Copy

`- ``eas diagnostics`

### `eas env:create [ENVIRONMENT]`

Create an environment variable for the current project or account.

Usage

Terminal

`- ``eas env:create [ENVIRONMENT] [--name <value>] [--value <value>] [--force] [--type string|file] [--visibility`

`plaintext|sensitive|secret] [--scope project|account] [--environment <value>...] [--non-interactive]`

Argument

  * `[ENVIRONMENT]` Environment to create the variable in. Default environments are 'production', 'preview', and 'development'.


Flags

  * `--environment=<value>...` Environment variable's environment, for example, 'production', 'preview', 'development'.
  * `--force` Overwrite existing variable.
  * `--name=<value>` Name of the variable.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--scope=<option>` [default: project] Scope for the variable <options: project|account>.
  * `--type=<option>` The type of variable <options: string|file>.
  * `--value=<value>` Text value or the variable.
  * `--visibility=<option>` Visibility of the variable <options: plaintext|sensitive|secret>.


### `eas env:delete [ENVIRONMENT]`

Delete an environment variable for the current project or account.

Usage

Terminal

`- ``eas env:delete [ENVIRONMENT] [--variable-name <value>] [--variable-environment <value>] [--scope`

`project|account] [--non-interactive]`

Argument

  * `[ENVIRONMENT]` Current environment of the variable to delete. Default environments are 'production', 'preview', and 'development'.


Flags

  * `--non-interactive` Run the command in non-interactive mode.
  * `--scope=<option>` [default: project] Scope for the variable <options: project|account>.
  * `--variable-environment=<value>` Current environment of the variable to delete.
  * `--variable-name=<value>` Name of the variable to delete.


### `eas env:exec ENVIRONMENT BASH_COMMAND`

Execute a command with environment variables from the selected environment.

Usage

Terminal

Copy

`- ``eas env:exec ENVIRONMENT BASH_COMMAND [--non-interactive]`

Arguments

  * `ENVIRONMENT` Environment to execute the command in. Default environments are 'production', 'preview', and 'development'.
  * `BASH_COMMAND` Bash command to execute with the environment variables from the environment.


Flag

  * `--non-interactive` Run the command in non-interactive mode.


### `eas env:get [ENVIRONMENT]`

View an environment variable for the current project or account.

Usage

Terminal

`- ``eas env:get [ENVIRONMENT] [--variable-name <value>] [--variable-environment <value>] [--format`

`long|short] [--scope project|account] [--non-interactive]`

Argument

  * `[ENVIRONMENT]` Current environment of the variable. Default environments are 'production', 'preview', and 'development'.


Flags

  * `--format=<option>` [default: short] Output format <options: long|short>.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--scope=<option>` [default: project] Scope for the variable <options: project|account>.
  * `--variable-environment=<value>` Current environment of the variable.
  * `--variable-name=<value>` Name of the variable.


### `eas env:list [ENVIRONMENT]`

List environment variables for the current project or account.

Usage

Terminal

`- ``eas env:list [ENVIRONMENT] [--include-sensitive] [--include-file-content] [--environment <value>...]`

`[--format long|short] [--scope project|account]`

Argument

  * `[ENVIRONMENT]` Environment to list the variables from. Default environments are 'production', 'preview', and 'development'.


Flags

  * `--environment=<value>...` Environment variable's environment, for example, 'production', 'preview', 'development'.
  * `--format=<option>` [default: short] Output format <options: long|short>.
  * `--include-file-content` Display files content in the output.
  * `--include-sensitive` Display sensitive values in the output.
  * `--scope=<option>` [default: project] Scope for the variable <options: project|account>.


### `eas env:pull [ENVIRONMENT]`

Pull environment variables for the selected environment to .env file.

Usage

Terminal

Copy

`- ``eas env:pull [ENVIRONMENT] [--non-interactive] [--environment <value>] [--path <value>]`

Argument

  * `[ENVIRONMENT]` Environment to pull variables from. Default environments are 'production', 'preview', and 'development'.


Flags

  * `--environment=<value>` Environment variable's environment, for example, 'production', 'preview', 'development'.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--path=<value>` [default: .env.local] Path to the result `.env` file.


### `eas env:push [ENVIRONMENT]`

Push environment variables from .env file to the selected environment.

Usage

Terminal

Copy

`- ``eas env:push [ENVIRONMENT] [--environment <value>...] [--path <value>] [--force]`

Argument

  * `[ENVIRONMENT]` Environment to push variables to. Default environments are 'production', 'preview', and 'development'.


Flags

  * `--environment=<value>...` Environment variable's environment, for example, 'production', 'preview', 'development'.
  * `--force` Skip confirmation and automatically override existing variables.
  * `--path=<value>` [default: .env.local] Path to the input `.env` file.


### `eas env:update [ENVIRONMENT]`

Update an environment variable on the current project or account.

Usage

Terminal

`- ``eas env:update [ENVIRONMENT] [--variable-name <value>] [--variable-environment <value>] [--name <value>]`

`[--value <value>] [--type string|file] [--visibility plaintext|sensitive|secret] [--scope project|account]``[--environment <value>...] [--non-interactive]`

Argument

  * `[ENVIRONMENT]` Current environment of the variable to update. Default environments are 'production', 'preview', and 'development'.


Flags

  * `--environment=<value>...` Environment variable's environment, for example, 'production', 'preview', 'development'.
  * `--name=<value>` New name of the variable.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--scope=<option>` [default: project] Scope for the variable <options: project|account>.
  * `--type=<option>` The type of variable <options: string|file>.
  * `--value=<value>` New value or the variable.
  * `--variable-environment=<value>` Current environment of the variable to update.
  * `--variable-name=<value>` Current name of the variable.
  * `--visibility=<option>` Visibility of the variable <options: plaintext|sensitive|secret>.


### `eas fingerprint:compare [HASH1] [HASH2]`

Compare fingerprints of the current project, builds, and updates.

Usage

Terminal

`- ``eas fingerprint:compare [HASH1...] [HASH2...] [--build-id <value>...] [--update-id <value>...] [--open]`

`[--environment <value>] [--json] [--non-interactive]`

Arguments

  * `[HASH1...]` If provided alone, HASH1 is compared against the current project's fingerprint.
  * `[HASH2...]` If two hashes are provided, HASH1 is compared against HASH2.


Flags

  * `--build-id=<value>...` Compare the fingerprint with the build with the specified ID.
  * `--environment=<value>` If generating a fingerprint from the local directory, use the specified environment.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--open` Open the fingerprint comparison in the browser.
  * `--update-id=<value>...` Compare the fingerprint with the update with the specified ID.


Examples

Terminal

`- ``eas fingerprint:compare # Compare fingerprints in interactive mode`




`- ``eas fingerprint:compare <FINGERPRINT-HASH> # Compare fingerprint against local directory`




`- ``eas fingerprint:compare <FINGERPRINT-HASH-1> <FINGERPRINT-HASH-2> # Compare provided fingerprints`




`- ``eas fingerprint:compare --build-id <BUILD-ID> # Compare fingerprint from build against local directory`




`- ``eas fingerprint:compare --build-id <BUILD-ID> --environment production # Compare fingerprint from build against local directory with the "production" environment`




`- ``eas fingerprint:compare --build-id <BUILD-ID-1> --build-id <BUILD-ID-2> # Compare fingerprint from a build against another build`




`- ``eas fingerprint:compare --build-id <BUILD-ID> --update-id <UPDATE-ID> # Compare fingerprint from build against fingerprint from update`




`- ``eas fingerprint:compare <FINGERPRINT-HASH> --update-id <UPDATE-ID> # Compare fingerprint from update against provided fingerprint`

### `eas fingerprint:generate`

Generate fingerprints from the current project.

Usage

Terminal

Copy

`- ``eas fingerprint:generate [-p android|ios] [--environment <value> | -e <value>] [--json] [--non-interactive]`

Flags

  * `-e, --build-profile=<value>` Name of the build profile from eas.json.
  * `-p, --platform=<option>` <options: android|ios>.
  * `--environment=<value>` Environment variable's environment, for example, 'production', 'preview', 'development'.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


Examples

Terminal

`- ``eas fingerprint:generate # Generate fingerprint in interactive mode`




`- ``eas fingerprint:generate --build-profile preview # Generate a fingerprint using the "preview" build profile`




`- ``eas fingerprint:generate --environment preview # Generate a fingerprint using the "preview" environment`




`- ``eas fingerprint:generate --json --non-interactive --platform android # Output fingerprint json to stdout`

### `eas help [COMMAND]`

Display help for eas.

Usage

Terminal

Copy

`- ``eas help [COMMAND...] [-n]`

Argument

  * `[COMMAND...]` Command to show help for.


Flag

  * `-n, --nested-commands` Include all nested commands in the output.


### `eas init`

Create or link an EAS project.

Usage

Terminal

Copy

`- ``eas init [--id <value>] [--force] [--non-interactive]`

Flags

  * `--force` Whether to create a new project/link an existing project without additional prompts or overwrite any existing project ID when running with `--id` flag.
  * `--id=<value>` ID of the EAS project to link.
  * `--non-interactive` Run the command in non-interactive mode.


Alias

Terminal

Copy

`- ``eas init`

### `eas init:onboarding [TARGET_PROJECT_DIRECTORY]`

Continue onboarding process started on the <https://expo.new> website.

Usage

Terminal

Copy

`- ``eas init:onboarding [TARGET_PROJECT_DIRECTORY]`

Aliases

Terminal

`- ``eas init:onboarding`

`- ``eas onboarding`

### `eas integrations:asc:connect`

Connect a project to an App Store Connect app.

Usage

Terminal

`- ``eas integrations:asc:connect [--api-key-id <value>] [--asc-app-id <value>] [--bundle-id <value>] [--json]`

`[--non-interactive]`

Flags

  * `--api-key-id=<value>` Apple App Store Connect API Key ID.
  * `--asc-app-id=<value>` App Store Connect app identifier.
  * `--bundle-id=<value>` Filter discovered apps by bundle identifier.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas integrations:asc:disconnect`

Disconnect the current project from its App Store Connect app.

Usage

Terminal

Copy

`- ``eas integrations:asc:disconnect [--yes] [--json] [--non-interactive]`

Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--yes` Skip confirmation prompt.


### `eas integrations:asc:status`

Show the App Store Connect app link status for the current project.

Usage

Terminal

Copy

`- ``eas integrations:asc:status [--json] [--non-interactive]`

Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas integrations:convex:connect`

Connect Convex to your Expo project.

Usage

Terminal

`- ``eas integrations:convex:connect [--non-interactive] [--region aws-us-east-1|aws-eu-west-1] [--team-name <value>]`

`[--project-name <value>]`

Flags

  * `--non-interactive` Run the command in non-interactive mode.
  * `--project-name=<value>` Name for the Convex project (defaults to app slug).
  * `--region=<option>` Convex deployment region (for example, aws-us-east-1, aws-eu-west-1) <options: aws-us-east-1|aws-eu-west-1>.
  * `--team-name=<value>` Name for the new Convex team (defaults to EAS account name).


### `eas integrations:convex:dashboard`

Open the Convex dashboard for the linked Convex project.

Usage

Terminal

Copy

`- ``eas integrations:convex:dashboard`

### `eas integrations:convex:project`

Display the Convex project linked to the current Expo app.

Usage

Terminal

Copy

`- ``eas integrations:convex:project`

### `eas integrations:convex:project:delete`

Remove the Convex project link for the current Expo app from EAS servers.

Usage

Terminal

Copy

`- ``eas integrations:convex:project:delete [--non-interactive] [-y]`

Flags

  * `-y, --yes` Skip confirmation prompt.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas integrations:convex:team`

Display Convex teams linked to the current Expo app's owner account.

Usage

Terminal

Copy

`- ``eas integrations:convex:team`

### `eas integrations:convex:team:delete [CONVEX_TEAM]`

Remove a Convex team link from the current Expo app owner account's EAS servers.

Usage

Terminal

Copy

`- ``eas integrations:convex:team:delete [CONVEX_TEAM] [--non-interactive] [-y]`

Argument

  * `[CONVEX_TEAM]` Slug of the Convex team to remove.


Flags

  * `-y, --yes` Skip confirmation prompt.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas integrations:convex:team:invite [CONVEX_TEAM]`

Send a Convex team invitation to your verified email address.

Usage

Terminal

Copy

`- ``eas integrations:convex:team:invite [CONVEX_TEAM] [--non-interactive]`

Argument

  * `[CONVEX_TEAM]` Slug of the Convex team to invite yourself to.


Flag

  * `--non-interactive` Run the command in non-interactive mode.


### `eas login`

Log in with your Expo account.

Usage

Terminal

Copy

`- ``eas login [-s] [-b]`

Flags

  * `-b, --[no-]browser` Log in with your browser (default; use `--no-browser` for CLI-based login).
  * `-s, --sso` Log in with SSO.


Alias

Terminal

Copy

`- ``eas login`

### `eas logout`

Log out.

Usage

Terminal

Copy

`- ``eas logout`

Alias

Terminal

Copy

`- ``eas logout`

### `eas metadata:lint`

Validate the local store configuration.

Usage

Terminal

Copy

`- ``eas metadata:lint [--json] [--profile <value>]`

Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`.
  * `--profile=<value>` Name of the submit profile from eas.json. Defaults to "production" if defined in eas.json.


### `eas metadata:pull`

Generate the local store configuration from the app stores.

Usage

Terminal

Copy

`- ``eas metadata:pull [-e <value>] [--non-interactive]`

Flags

  * `-e, --profile=<value>` Name of the submit profile from eas.json. Defaults to "production" if defined in eas.json.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas metadata:push`

Sync the local store configuration to the app stores.

Usage

Terminal

Copy

`- ``eas metadata:push [-e <value>] [--non-interactive]`

Flags

  * `-e, --profile=<value>` Name of the submit profile from eas.json. Defaults to "production" if defined in eas.json.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas new [PATH]`

Create a new project configured with Expo Application Services (EAS).

Usage

Terminal

Copy

`- ``eas new [PATH] [-p bun|npm|pnpm|yarn]`

Argument

  * `[PATH]` Path to create the project (defaults to current directory).


Flag

  * `-p, --package-manager=<option>` [default: npm] Package manager to use for installing dependencies <options: bun|npm|pnpm|yarn>.


Alias

Terminal

Copy

`- ``eas new`

### `eas onboarding [TARGET_PROJECT_DIRECTORY]`

Continue onboarding process started on the <https://expo.new> website.

Usage

Terminal

Copy

`- ``eas onboarding [TARGET_PROJECT_DIRECTORY]`

Aliases

Terminal

`- ``eas init:onboarding`

`- ``eas onboarding`

### `eas open`

Open the project page in a web browser.

Usage

Terminal

Copy

`- ``eas open`

### `eas project:info`

Information about the current project.

Usage

Terminal

Copy

`- ``eas project:info`

### `eas project:init`

Create or link an EAS project.

Usage

Terminal

Copy

`- ``eas project:init [--id <value>] [--force] [--non-interactive]`

Flags

  * `--force` Whether to create a new project/link an existing project without additional prompts or overwrite any existing project ID when running with `--id` flag.
  * `--id=<value>` ID of the EAS project to link.
  * `--non-interactive` Run the command in non-interactive mode.


Alias

Terminal

Copy

`- ``eas init`

### `eas project:new [PATH]`

Create a new project configured with Expo Application Services (EAS).

Usage

Terminal

Copy

`- ``eas project:new [PATH] [-p bun|npm|pnpm|yarn]`

Argument

  * `[PATH]` Path to create the project (defaults to current directory).


Flag

  * `-p, --package-manager=<option>` [default: npm] Package manager to use for installing dependencies <options: bun|npm|pnpm|yarn>.


Alias

Terminal

Copy

`- ``eas new`

### `eas project:onboarding [TARGET_PROJECT_DIRECTORY]`

Continue onboarding process started on the <https://expo.new> website.

Usage

Terminal

Copy

`- ``eas project:onboarding [TARGET_PROJECT_DIRECTORY]`

Aliases

Terminal

`- ``eas init:onboarding`

`- ``eas onboarding`

### `eas submit`

Submit app binary to App Store and/or Play Store.

Usage

Terminal

`- ``eas submit [-p android|ios|all] [-e <value>] [--latest | --id <value> | --path <value> | --url <value>]`

`[--what-to-test <value>] [--verbose] [--wait] [--verbose-fastlane] [-g <value>...] [--non-interactive]`

Flags

  * `-e, --profile=<value>` Name of the submit profile from eas.json. Defaults to "production" if defined in eas.json.
  * `-g, --groups=<value>...` Internal TestFlight testing groups to add the build to (iOS only). Learn more: <https://developer.apple.com/help/app-store-connect/test-a-beta-version/add-internal-testers>.
  * `-p, --platform=<option>` <options: android|ios|all>.
  * `--id=<value>` ID of the build to submit.
  * `--latest` Submit the latest build for specified platform.
  * `--non-interactive` Run command in non-interactive mode.
  * `--path=<value>` Path to the .apk/.aab/.ipa file.
  * `--url=<value>` App archive url.
  * `--verbose` Always print logs from EAS Submit.
  * `--verbose-fastlane` Enable verbose logging for the submission process.
  * `--[no-]wait` Wait for submission to complete.
  * `--what-to-test=<value>` Sets the "What to test" information in TestFlight (iOS only).


Alias

Terminal

Copy

`- ``eas build:submit`

### `eas update`

Publish an update group.

Usage

Terminal

`- ``eas update [--branch <value>] [--channel <value>] [-m <value>] [--input-dir <value>] [--skip-bundler]`

`[--clear-cache] [--emit-metadata] [--rollout-percentage <value>] [-p android|ios|all] [--auto] [--private-key-path``<value>] [--environment <value>] [--json] [--non-interactive]`

Flags

  * `-m, --message=<value>` A short message describing the update.
  * `-p, --platform=<option>` [default: all] <options: android|ios|all>.
  * `--auto` Use the current git branch and commit message for the EAS branch and update message.
  * `--branch=<value>` Branch to publish the update group on.
  * `--channel=<value>` Channel that the published update should affect.
  * `--clear-cache` Clear the bundler cache before publishing.
  * `--emit-metadata` Emit "eas-update-metadata.json" in the bundle folder with detailed information about the generated updates.
  * `--environment=<value>` Environment to use for the server-side defined EAS environment variables during command execution, for example, "production", "preview", "development". Required for projects using Expo SDK 55 or greater.
  * `--input-dir=<value>` [default: dist] Location of the bundle.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--private-key-path=<value>` File containing the PEM-encoded private key corresponding to the certificate in expo-updates' configuration. Defaults to a file named "private-key.pem" in the certificate's directory. Only relevant if you are using code signing: <https://docs.expo.dev/eas-update/code-signing/>.
  * `--rollout-percentage=<value>` Percentage of users this update should be immediately available to. Users not in the rollout will be served the previous latest update on the branch, even if that update is itself being rolled out. The specified number must be an integer between 1 and 100. When not specified, this defaults to 100.
  * `--skip-bundler` Skip running Expo CLI to bundle the app before publishing.


### `eas update:configure`

Configure the project to support EAS Update.

Usage

Terminal

Copy

`- ``eas update:configure [-p android|ios|all] [--environment <value>] [--non-interactive]`

Flags

  * `-p, --platform=<option>` [default: all] Platform to configure <options: android|ios|all>.
  * `--environment=<value>` Environment to use for the server-side defined EAS environment variables during command execution, for example, "production", "preview", "development".
  * `--non-interactive` Run the command in non-interactive mode.


### `eas update:delete GROUPID`

Delete all the updates in an update group.

Usage

Terminal

Copy

`- ``eas update:delete GROUPID [--json] [--non-interactive]`

Argument

  * `GROUPID` The ID of an update group to delete.


Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas update:edit [GROUPID]`

Edit all the updates in an update group.

Usage

Terminal

Copy

`- ``eas update:edit [GROUPID] [--rollout-percentage <value>] [--branch <value>] [--json] [--non-interactive]`

Argument

  * `[GROUPID]` The ID of an update group to edit.


Flags

  * `--branch=<value>` Branch for which to list updates to select from.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--rollout-percentage=<value>` Rollout percentage to set for a rollout update. The specified number must be an integer between 1 and 100.


### `eas update:insights GROUPID`

Display launch, crash, unique-user, and size insights for an update group.

Usage

Terminal

`- ``eas update:insights GROUPID [--platform ios|android] [--days <value> | --start <value> | --end <value>] [--json]`

`[--non-interactive]`

Argument

  * `GROUPID` The ID of an update group.


Flags

  * `--days=<value>` Show insights from the last N days (default 7, mutually exclusive with `--start`/`--end`).
  * `--end=<value>` End of insights time range (ISO date).
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--platform=<option>` Filter to a single platform. <options: ios|android>.
  * `--start=<value>` Start of insights time range (ISO date).


### `eas update:list`

View the recent updates.

Usage

Terminal

`- ``eas update:list [--branch <value> | --all] [-p android|ios|all] [--runtime-version <value>] [--offset`

`<value>] [--limit <value>] [--json] [--non-interactive]`

Flags

  * `-p, --platform=<option>` Filter updates by platform <options: android|ios|all>.
  * `--all` List updates on all branches.
  * `--branch=<value>` List updates only on this branch.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--limit=<value>` The number of items to fetch each query. Defaults to 25 and is capped at 50.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--offset=<value>` Start queries from specified index. Use for paginating results. Defaults to 0.
  * `--runtime-version=<value>` Filter updates by runtime version.


### `eas update:republish`

Roll back to an existing update.

Usage

Terminal

`- ``eas update:republish [--channel <value> | --branch <value> | --group <value>] [--destination-channel <value> |`

`--destination-branch <value>] [-m <value>] [-p android|ios|all] [--private-key-path <value>] [--rollout-percentage``<value>] [--json] [--non-interactive]`

Flags

  * `-m, --message=<value>` Short message describing the republished update group.
  * `-p, --platform=<option>` [default: all] <options: android|ios|all>.
  * `--branch=<value>` Branch name to select an update group to republish from.
  * `--channel=<value>` Channel name to select an update group to republish from.
  * `--destination-branch=<value>` Branch name to republish to if republishing to a different branch.
  * `--destination-channel=<value>` Channel name to select a branch to republish to if republishing to a different branch.
  * `--group=<value>` Update group ID to republish.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--private-key-path=<value>` File containing the PEM-encoded private key corresponding to the certificate in expo-updates' configuration. Defaults to a file named "private-key.pem" in the certificate's directory. Only relevant if you are using code signing: <https://docs.expo.dev/eas-update/code-signing/>.
  * `--rollout-percentage=<value>` Percentage of users this update should be immediately available to. Users not in the rollout will be served the previous latest update on the branch, even if that update is itself being rolled out. The specified number must be an integer between 1 and 100. When not specified, this defaults to 100.


### `eas update:revert-update-rollout`

Revert a rollout update for a project.

Usage

Terminal

`- ``eas update:revert-update-rollout [--channel <value> | --branch <value> | --group <value>] [-m <value>] [--private-key-path`

`<value>] [--json] [--non-interactive]`

Flags

  * `-m, --message=<value>` Short message describing the revert.
  * `--branch=<value>` Branch name to select an update group to revert the rollout update from.
  * `--channel=<value>` Channel name to select an update group to revert the rollout update from.
  * `--group=<value>` Rollout update group ID to revert.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--private-key-path=<value>` File containing the PEM-encoded private key corresponding to the certificate in expo-updates' configuration. Defaults to a file named "private-key.pem" in the certificate's directory. Only relevant if you are using code signing: <https://docs.expo.dev/eas-update/code-signing/>.


### `eas update:roll-back-to-embedded`

Roll back to the embedded update.

Usage

Terminal

`- ``eas update:roll-back-to-embedded [--branch <value>] [--channel <value>] [--runtime-version <value>] [--message <value>] [-p`

`android|ios|all] [--private-key-path <value>] [--json] [--non-interactive]`

Flags

  * `-p, --platform=<option>` [default: all] <options: android|ios|all>.
  * `--branch=<value>` Branch to publish the rollback to embedded update group on.
  * `--channel=<value>` Channel that the published rollback to embedded update should affect.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--message=<value>` A short message describing the rollback to embedded update.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--private-key-path=<value>` File containing the PEM-encoded private key corresponding to the certificate in expo-updates' configuration. Defaults to a file named "private-key.pem" in the certificate's directory. Only relevant if you are using code signing: <https://docs.expo.dev/eas-update/code-signing/>.
  * `--runtime-version=<value>` Runtime version that the rollback to embedded update should target.


### `eas update:rollback`

Roll back to an embedded update or an existing update. Users wishing to run this command non-interactively should instead execute "eas update:republish" or "eas update:roll-back-to-embedded".

Usage

Terminal

Copy

`- ``eas update:rollback [--private-key-path <value>]`

Flag

  * `--private-key-path=<value>` File containing the PEM-encoded private key corresponding to the certificate in expo-updates' configuration. Defaults to a file named "private-key.pem" in the certificate's directory. Only relevant if you are using code signing: <https://docs.expo.dev/eas-update/code-signing/>.


### `eas update:view GROUPID`

Update group details.

Usage

Terminal

Copy

`- ``eas update:view GROUPID [--insights] [--days <value> | --start <value> | --end <value>] [--json]`

Argument

  * `GROUPID` The ID of an update group.


Flags

  * `--days=<value>` Show insights from the last N days (default 7). Only used with `--insights`.
  * `--end=<value>` End of insights time range (ISO date). Only used with `--insights`.
  * `--insights` Also show insights (launches, crash rate, unique users, payload size) for the update group.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`.
  * `--start=<value>` Start of insights time range (ISO date). Only used with `--insights`.


### `eas upload`

Upload a local build and generate a sharable link.

Usage

Terminal

Copy

`- ``eas upload [-p ios|android] [--build-path <value>] [--fingerprint <value>] [--json] [--non-interactive]`

Flags

  * `-p, --platform=<option>` <options: ios|android>.
  * `--build-path=<value>` Path for the local build.
  * `--fingerprint=<value>` Fingerprint hash of the local build.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas webhook:create`

Create a webhook.

Usage

Terminal

Copy

`- ``eas webhook:create [--event BUILD|SUBMIT] [--url <value>] [--secret <value>] [--non-interactive]`

Flags

  * `--event=<option>` Event type that triggers the webhook <options: BUILD|SUBMIT>.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--secret=<value>` Secret used to create a hash signature of the request payload, provided in the 'Expo-Signature' header.
  * `--url=<value>` Webhook URL.


### `eas webhook:delete [ID]`

Delete a webhook.

Usage

Terminal

Copy

`- ``eas webhook:delete [ID] [--non-interactive]`

Argument

  * `[ID]` ID of the webhook to delete.


Flag

  * `--non-interactive` Run the command in non-interactive mode.


### `eas webhook:list`

List webhooks.

Usage

Terminal

Copy

`- ``eas webhook:list [--event BUILD|SUBMIT] [--json]`

Flags

  * `--event=<option>` Event type that triggers the webhook <options: BUILD|SUBMIT>.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`.


### `eas webhook:update`

Update a webhook.

Usage

Terminal

Copy

`- ``eas webhook:update --id <value> [--event BUILD|SUBMIT] [--url <value>] [--secret <value>] [--non-interactive]`

Flags

  * `--event=<option>` Event type that triggers the webhook <options: BUILD|SUBMIT>.
  * `--id=<value>` (required) Webhook ID.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--secret=<value>` Secret used to create a hash signature of the request payload, provided in the 'Expo-Signature' header.
  * `--url=<value>` Webhook URL.


### `eas webhook:view ID`

View a webhook.

Usage

Terminal

Copy

`- ``eas webhook:view ID`

Argument

  * `ID` ID of the webhook to view.


### `eas whoami`

Show the username you are logged in as.

Usage

Terminal

Copy

`- ``eas whoami`

Alias

Terminal

Copy

`- ``eas whoami`

### `eas worker:alias`

Assign deployment aliases.

Usage

Terminal

Copy

`- ``eas worker:alias [--prod] [--alias name] [--id xyz123] [--json] [--non-interactive]`

Flags

  * `--alias=name` Custom alias to assign to the existing deployment.
  * `--id=xyz123` Unique identifier of an existing deployment.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--prod` Promote an existing deployment to production.


Aliases

Terminal

`- ``eas worker:alias`

`- ``eas deploy:promote`

### `eas worker:alias:delete [ALIAS_NAME]`

Delete deployment aliases.

Usage

Terminal

Copy

`- ``eas worker:alias:delete [ALIAS_NAME] [--json] [--non-interactive]`

Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


Alias

Terminal

Copy

`- ``eas worker:alias:delete`

### `eas worker:delete [DEPLOYMENT_ID]`

Delete a deployment.

Usage

Terminal

Copy

`- ``eas worker:delete [DEPLOYMENT_ID] [--json] [--non-interactive]`

Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`. Implies `--non-interactive`.
  * `--non-interactive` Run the command in non-interactive mode.


Alias

Terminal

Copy

`- ``eas worker:delete`

### `eas workflow:cancel`

Cancel one or more workflow runs. If no workflow run IDs are provided, you will be prompted to select IN_PROGRESS runs to cancel.

Usage

Terminal

Copy

`- ``eas workflow:cancel [--non-interactive]`

Flag

  * `--non-interactive` Run the command in non-interactive mode.


### `eas workflow:create [NAME]`

Create a new workflow configuration YAML file.

Usage

Terminal

Copy

`- ``eas workflow:create [NAME] [--skip-validation]`

Argument

  * `[NAME]` Name of the workflow file (must end with .yml or .yaml).


Flag

  * `--skip-validation` If set, the workflow file will not be validated before being created.


### `eas workflow:logs [ID]`

View logs for a workflow run, selecting a job and step to view. You can pass in either a workflow run ID or a job ID. If no ID is passed in, you will be prompted to select from recent workflow runs for the current project.

Usage

Terminal

Copy

`- ``eas workflow:logs [ID] [--json] [--non-interactive] [--all-steps]`

Argument

  * `[ID]` ID of the workflow run or workflow job to view logs for.


Flags

  * `--all-steps` Print all logs, rather than prompting for a specific step. This will be automatically set when in non-interactive mode.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`.
  * `--non-interactive` Run the command in non-interactive mode.


### `eas workflow:run FILE`

Run an EAS workflow. The entire local project directory will be packaged and uploaded to EAS servers for the workflow run, unless the `--ref` flag is used.

Usage

Terminal

Copy

`- ``eas workflow:run FILE [--non-interactive] [--wait] [-F <value>...] [--ref <value>] [--json]`

Argument

  * `FILE` Path to the workflow file to run.


Flags

  * `-F, --input=<value>...` Set workflow inputs.
  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--ref=<value>` Git reference to run the workflow on.
  * `--[no-]wait` Wait for workflow run to complete. Defaults to false.


### `eas workflow:runs`

List recent workflow runs for this project, with their IDs, statuses, and timestamps.

Usage

Terminal

`- ``eas workflow:runs [--workflow <value>] [--status ACTION_REQUIRED|CANCELED|FAILURE|IN_PROGRESS|NEW|SUCCESS]`

`[--json] [--limit <value>]`

Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`.
  * `--limit=<value>` The number of items to fetch each query. Defaults to 10 and is capped at 100.
  * `--status=<option>` If present, filter the returned runs to select those with the specified status <options: ACTION_REQUIRED|CANCELED|FAILURE|IN_PROGRESS|NEW|SUCCESS>.
  * `--workflow=<value>` If present, the query will only return runs for the specified workflow file name.


### `eas workflow:status [WORKFLOW_RUN_ID]`

Show the status of an existing workflow run. If no run ID is provided, you will be prompted to select from recent workflow runs for the current project.

Usage

Terminal

Copy

`- ``eas workflow:status [WORKFLOW_RUN_ID] [--non-interactive] [--wait] [--json]`

Argument

  * `[WORKFLOW_RUN_ID]` A workflow run ID.


Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`.
  * `--non-interactive` Run the command in non-interactive mode.
  * `--[no-]wait` Wait for workflow run to complete. Defaults to false.


### `eas workflow:validate PATH`

Validate a workflow configuration yaml file.

Usage

Terminal

Copy

`- ``eas workflow:validate PATH [--non-interactive]`

Argument

  * `PATH` Path to the workflow configuration YAML file (must end with .yml or .yaml).


Flag

  * `--non-interactive` Run the command in non-interactive mode.


### `eas workflow:view [ID]`

View details for a workflow run, including jobs. If no run ID is provided, you will be prompted to select from recent workflow runs for the current project.

Usage

Terminal

Copy

`- ``eas workflow:view [ID] [--json] [--non-interactive]`

Argument

  * `[ID]` ID of the workflow run to view.


Flags

  * `--json` Enable JSON output, non-JSON messages will be printed to `stderr`.
  * `--non-interactive` Run the command in non-interactive mode.