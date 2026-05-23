---
title: "Expo Skills for AI agents"
url: https://docs.expo.dev/skills
---

# Expo Skills for AI agents

# Expo Skills for AI agents

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/skills.mdx)

Copy page

A list of official AI agent skills provided by Expo for building, deploying, and debugging Expo and React Native apps.

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/skills.mdx)

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

Expo Skills are structured instruction files that teach AI agents how to build, deploy, and debug Expo and React Native apps accurately and efficiently. They work with Claude Code, Cursor, Codex, and other AI agents.

## Install Expo Skills

Claude Code

Cursor

Other agents

Run the following commands to add and install Expo Skills from the plugin marketplace:

Terminal

`- ``/plugin marketplace add expo/skills`




`- ``/plugin install expo`

If you have already installed Expo Skills for Claude Code, Codex, or another agent, recent versions of Cursor import them automatically. Open Settings > Rules, Skills, Subagents, make sure Include third-party Plugins, Skills, and other configs is enabled (it is on by default), and the Expo Skills appear in the Skills list.

If you have not installed Expo Skills yet, run one of the following with the [skills CLI](https://skills.sh/docs/cli):

Terminalnpmyarnpnpmbun

Copy

`- ``npx skills add expo/skills`

Then reopen Cursor and verify the skills appear under Settings > Rules, Skills, Subagents > Skills.

> Skills in Cursor are not shown in the slash command (`/`) menu. They work via auto-discovery when you ask the agent Expo-related questions.

Use the [skills CLI](https://skills.sh/docs/cli) to add Expo Skills to any compatible agent:

Terminalnpmyarnpnpmbun

Copy

`- ``npx skills add expo/skills`

## Available Expo Skills

The following skills are available in the `expo` plugin:

Skill| Description
---|---
[`building-native-ui`](https://github.com/expo/skills/blob/main/plugins/expo/skills/building-native-ui/SKILL.md)| Complete guide for building beautiful apps with Expo Router. Covers fundamentals, styling, components, navigation, animations, patterns, and native tabs.
[`eas-update-insights`](https://github.com/expo/skills/blob/main/plugins/expo/skills/eas-update-insights/SKILL.md)| Check the health of published EAS Updates: crash rates, install/launch counts, unique users, payload size, and the split between embedded and OTA users per channel. Use when the user asks how an update is performing, whether a rollout is healthy, how many users are on the embedded build vs OTA, or wants to gate CI on update health.
[`expo-api-routes`](https://github.com/expo/skills/blob/main/plugins/expo/skills/expo-api-routes/SKILL.md)| Guidelines for creating API routes in Expo Router with EAS Hosting.
[`expo-brownfield`](https://github.com/expo/skills/blob/main/plugins/expo/skills/expo-brownfield/SKILL.md)| Integrate Expo and React Native into an existing native iOS or Android app. Use when the user mentions brownfield, embedding React Native in a native app, AAR/XCFramework, or adding Expo to an existing Kotlin/Swift project. Covers both the isolated approach and the integrated approach.
[`expo-cicd-workflows`](https://github.com/expo/skills/blob/main/plugins/expo/skills/expo-cicd-workflows/SKILL.md)| Helps understand and write EAS workflow YAML files for Expo projects. Use this skill when the user asks about CI/CD or workflows in an Expo or EAS context, mentions .eas/workflows/, or wants help with EAS build pipelines or deployment automation.
[`expo-deployment`](https://github.com/expo/skills/blob/main/plugins/expo/skills/expo-deployment/SKILL.md)| Deploying Expo apps to iOS App Store, Android Play Store, web hosting, and API routes.
[`expo-dev-client`](https://github.com/expo/skills/blob/main/plugins/expo/skills/expo-dev-client/SKILL.md)| Build and distribute Expo development clients locally or via TestFlight.
[`expo-module`](https://github.com/expo/skills/blob/main/plugins/expo/skills/expo-module/SKILL.md)| Guide for creating and writing Expo native modules and views using the Expo Modules API (Swift, Kotlin, TypeScript). Covers module definition DSL, native views, shared objects, config plugins, lifecycle hooks, autolinking, and type system. Use when building or modifying native modules for Expo.
[`expo-tailwind-setup`](https://github.com/expo/skills/blob/main/plugins/expo/skills/expo-tailwind-setup/SKILL.md)| Set up Tailwind CSS v4 in Expo with react-native-css and NativeWind v5 for universal styling.
[`expo-ui-jetpack-compose`](https://github.com/expo/skills/blob/main/plugins/expo/skills/expo-ui-jetpack-compose/SKILL.md)| `@expo/ui/jetpack-compose` package lets you use Jetpack Compose Views and modifiers in your app.
[`expo-ui-swift-ui`](https://github.com/expo/skills/blob/main/plugins/expo/skills/expo-ui-swift-ui/SKILL.md)| `@expo/ui/swift-ui` package lets you use SwiftUI Views and modifiers in your app.
[`native-data-fetching`](https://github.com/expo/skills/blob/main/plugins/expo/skills/native-data-fetching/SKILL.md)| Use when implementing or debugging ANY network request, API call, or data fetching. Covers fetch API, React Query, SWR, error handling, caching, offline support, and Expo Router data loaders (`useLoaderData`).
[`upgrading-expo`](https://github.com/expo/skills/blob/main/plugins/expo/skills/upgrading-expo/SKILL.md)| Guidelines for upgrading Expo SDK versions and fixing dependency issues.
[`use-dom`](https://github.com/expo/skills/blob/main/plugins/expo/skills/use-dom/SKILL.md)| Use Expo DOM components to run web code in a webview on native and as-is on web. Migrate web code to native incrementally.

## Example prompts

Try the following prompts after installing Expo Skills. Your AI agent will automatically use the appropriate skill:

Example prompt| Skill used
---|---
Build a settings screen with a form and navigation| `building-native-ui`
Set up Tailwind CSS in my Expo project| `expo-tailwind-setup`
Embed a recharts chart in my native app using web code| `use-dom`
Add a SwiftUI picker component to my Expo app| `expo-ui-swift-ui`
Use Material Design 3 components with Jetpack Compose| `expo-ui-jetpack-compose`
How do I deploy my Expo app to the Apple App Store?| `expo-deployment`
Create a CI/CD workflow that builds on every PR| `expo-cicd-workflows`
Upgrade my project to the latest Expo SDK| `upgrading-expo`

## Additional resources

[`expo/skills` GitHub repositoryBrowse the source for all available Expo Skills, or report issues.](https://github.com/expo/skills) [Expo MCP ServerCompanion AI tooling that gives coding agents direct access to Expo and EAS services.](/eas/ai/mcp)