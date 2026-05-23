---
title: "ExpoDevMenu"
url: https://docs.expo.dev/versions/latest/sdk/dev-menu
---

# ExpoDevMenu

# Expo DevMenu

A library that provides a developer menu for debug builds.

Android

iOS

tvOS

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-dev-menu)[npm](https://www.npmjs.com/package/expo-dev-menu)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-dev-menu/CHANGELOG.md)

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

The `expo-dev-menu` can be used as a standalone library in any Expo project. It is especially useful in [brownfield apps](/versions/latest/sdk/brownfield) that don't need the full [`expo-dev-client`](/versions/latest/sdk/dev-client) launcher interface.

`expo-dev-menu` provides a developer menu UI for React Native apps that includes:

  * A powerful and extensible menu UI accessible via shake gesture or three-finger long press
  * Quick access to common development actions
  * Support for custom menu items to extend functionality


## Installation

Terminal

Copy

`- ``npx expo install expo-dev-menu`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Usage

Once installed, the developer menu is available in your debug builds. You can open it by:

  * Shake gesture: Shake your device
  * Three-finger long press: Long press with three fingers on the screen
  * Programmatically: Call `DevMenu.openMenu()` from your code


## Extending the dev menu

The dev menu can be extended to include extra buttons by using the `registerDevMenuItems` API:


    import { registerDevMenuItems } from 'expo-dev-menu';

    const devMenuItems = [
      {
        name: 'My Custom Button',
        callback: () => console.log('Hello world!'),
      },
    ];

    registerDevMenuItems(devMenuItems);


This will create a new section in the dev menu that includes the buttons you have registered:

> Note: Subsequent calls of `registerDevMenuItems` will override all previous entries.

## Using with expo-dev-client

If you are using [development builds](/develop/development-builds/introduction), install `expo-dev-client` instead. It includes `expo-dev-menu` along with additional development tools:

  * A configurable launcher UI for switching between development servers
  * Improved debugging tools
  * Support for loading updates from [EAS Update](/eas-update/introduction)


Terminal

Copy

`- ``npx expo install expo-dev-client`

For more information, check the [`expo-dev-client` reference](/versions/latest/sdk/dev-client).

## API


    import * as DevMenu from 'expo-dev-menu';


## Methods

### `DevMenu.closeMenu()`

Android

iOS

tvOS

A method that closes development client menu when called.

Returns:

`void`

### `DevMenu.hideMenu()`

Android

iOS

tvOS

A method that hides development client menu when called.

Returns:

`void`

### `DevMenu.openMenu()`

Android

iOS

tvOS

A method that opens development client menu when called.

Returns:

`void`

### `DevMenu.registerDevMenuItems(items)`

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