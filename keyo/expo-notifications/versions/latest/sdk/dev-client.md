---
title: "ExpoDevClient"
url: https://docs.expo.dev/versions/latest/sdk/dev-client
---

# ExpoDevClient

# Expo DevClient

A library that allows creating a development build and includes useful development tools.

Android

iOS

tvOS

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-dev-client)[npm](https://www.npmjs.com/package/expo-dev-client)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-dev-client/CHANGELOG.md)

Bundled version:

~56.0.14

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`expo-dev-client` adds various useful development tools to your debug builds:

  * A configurable launcher UI, so you can launch updates (such as from [PR previews](/develop/development-builds/development-workflows#pr-previews)) and switch between development servers without needing to recompile the native app
  * Improved debugging tools (such as support for [inspecting network requests](/debugging/tools#inspecting-network-requests))
  * [A powerful and extensible developer menu UI](/debugging/tools#developer-menu)


Expo documentation refers to debug builds that include `expo-dev-client` as [development builds](/develop/development-builds/introduction).

## Installation

Terminal

Copy

`- ``npx expo install expo-dev-client`

If you are installing this in an [existing React Native app](/bare/overview), start by installing [`expo`](/bare/installing-expo-modules) in your project. Then, follow the instructions from [Install `expo-dev-client` in an existing React Native project](/bare/install-dev-builds-in-bare).

## Configuration in app config

You can configure development client launcher using its built-in [config plugin](/config-plugins/introduction) if you use config plugins in your project ([Continuous Native Generation (CNG)](/workflow/continuous-native-generation)). The plugin allows you to configure various properties that cannot be set at runtime and require building a new app binary to take effect. If your app does not use CNG, then you'll need to manually configure the library.

### Example app.json with config plugin

app.json

Copy


    {
      "expo": {
        "plugins": [
          [
            "expo-dev-client",
            {
              "launchMode": "most-recent",
              "defaultLaunchURL": "http://localhost:8081",
              "android": {
                "defaultLaunchURL": "http://10.0.0.2:8081"
              }
            }
          ]
        ]
      }
    }


### Configurable properties

Name| Default| Description
---|---|---
`launchMode`| `"most-recent"`| Determines whether to launch the most recently opened project or navigate to the launcher screen.

  * `most-recent` \- Attempt to launch directly into a previously opened project and if unable to connect, fall back to the launcher screen.
  * `launcher` \- Opens the launcher screen.


`addGeneratedScheme`| `true`| By default, `expo-dev-client` will register a custom URL scheme to open a project. Set this property to `false` to disable this scheme.
`defaultLaunchURL`| -| Launch directly into this URL instead of navigating to launcher screen. If `launchMode` is set to `most-recent`, then launcher will use the `defaultLaunchURL` as a fallback.
`android.launchMode`| `"most-recent"`| Only for: Android
Determines whether to launch the most recently opened project or navigate to the launcher screen on Android. Overrides the top-level `launchMode` setting for Android only.

  * `most-recent` \- Attempt to launch directly into a previously opened project and if unable to connect, fall back to the launcher screen.
  * `launcher` \- Opens the launcher screen.


`ios.launchMode`| `"most-recent"`| Only for: iOS
Determines whether to launch the most recently opened project or navigate to the launcher screen on iOS. Overrides the top-level `launchMode` setting for iOS only.

  * `most-recent` \- Attempt to launch directly into a previously opened project and if unable to connect, fall back to the launcher screen.
  * `launcher` \- Opens the launcher screen.


`android.defaultLaunchURL`| -| Only for: Android
Instead of navigating to launcher screen launch directly into this URL. If `launchMode` is set to `most-recent`, then launcher will use the `defaultLaunchURL` as a fallback.
`ios.defaultLaunchURL`| -| Only for: iOS
Instead of navigating to launcher screen launch directly into this URL. If `launchMode` is set to `most-recent`, then launcher will use the `defaultLaunchURL` as a fallback.

## TV support

  * This library is only supported for TV in SDK 54 and later.
    * Android TV: All operations are supported, similar to an Android phone.
    * Apple TV: Basic operations with a local or tunneled packager are supported. Authentication to EAS and listing of EAS builds and updates is not yet supported.


## API


    import * as DevClient from 'expo-dev-client';


## Methods

### `DevClient.closeMenu()`

Android

iOS

tvOS

A method that closes development client menu when called.

Returns:

`void`

### `DevClient.hideMenu()`

Android

iOS

tvOS

A method that hides development client menu when called.

Returns:

`void`

### `DevClient.openMenu()`

Android

iOS

tvOS

A method that opens development client menu when called.

Returns:

`void`

### `DevClient.registerDevMenuItems(items)`

Android

iOS

tvOS

Parameter| Type
---|---
items| `ExpoDevMenuItem[]`




A method that allows to specify custom entries in the development client menu.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

## Types

### `ExpoDevMenuItem`

Android

iOS

tvOS

An object representing the custom development client menu entry.

Property| Type| Description
---|---|---
callback| `() => void`| Callback to fire, when user selects an item.
name| `string`| Name of the entry, will be used as label.
shouldCollapse(optional)| `boolean`| A boolean specifying if the menu should close after the user interaction.Default:`false`