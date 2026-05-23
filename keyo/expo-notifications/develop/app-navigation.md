---
title: "Navigation in Expo and React Native apps"
url: https://docs.expo.dev/develop/app-navigation
---

# Navigation in Expo and React Native apps

# Navigation in Expo and React Native apps

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/develop/app-navigation.mdx)

Copy page

Learn about the recommended approach for integrating navigation in an Expo and React Native project.

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/develop/app-navigation.mdx)

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

The core React Native library does not include a built-in navigation solution, so you can choose a navigation library that best fits your needs. For Expo and React Native apps, it is generally a choice between [React Navigation](https://reactnavigation.org/) or [Expo Router](/router/introduction).

## Why React Native apps need a navigation library

React Native core includes basic UI components, touch handling, device APIs and networking, but excludes, among other things, storage, camera, maps, most device sensors, and navigation! These are intended to be covered by community libraries.

## React Navigation

React Navigation is a component-based navigation library widely used across the React Native ecosystem. It lets you compose stack, tab, and drawer navigators entirely in code so you can implement complex flows, custom transitions, and app-specific UX patterns.

The library offers platform-specific look-and-feel with smooth animations and gestures, unified mobile and web routing, automatic deep links, type routes with static configuration, and is highly customizable.

[React Navigation: Getting startedLearn how to get started with React Navigation.](https://reactnavigation.org/docs/getting-started)

## Expo Router (recommended for Expo projects)

Expo Router is a file-based routing library for Expo and React Native projects. By following the app directory convention, it turns files into routes and is integrated with Expo for [Expo CLI](/more/expo-cli) and bundling without additional setup. The library also adds features such as typed routes, dynamic routes, lazy bundling in development, static rendering for the web, and automatic deep linking.

New Expo projects created with `npx create-expo-app@latest --template default@sdk-56` include Expo Router by default.

[Introduction to Expo RouterExpo Router is an open-source routing library for Universal React Native applications built with Expo.](/router/introduction) [InstallationLearn how to quickly get started by creating a new project with Expo Router or adding the library to an existing project.](/router/installation) [Core conceptsLearn about the core concepts of file-based routing in Expo.](/router/basics/core-concepts)