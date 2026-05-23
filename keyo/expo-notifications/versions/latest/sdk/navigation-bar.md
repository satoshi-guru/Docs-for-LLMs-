---
title: "ExpoNavigationBar"
url: https://docs.expo.dev/versions/latest/sdk/navigation-bar
---

# ExpoNavigationBar

# Expo NavigationBar

A library that provides access to various interactions with the native navigation bar on Android.

Android

Included in Expo Go

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-navigation-bar)[npm](https://www.npmjs.com/package/expo-navigation-bar)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-navigation-bar/CHANGELOG.md)

Bundled version:

~56.0.3

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`expo-navigation-bar` provides a component and an imperative API for controlling the app's navigation bar on Android devices, allowing you to change the color of its buttons or hide it.

## Installation

Terminal

Copy

`- ``npx expo install expo-navigation-bar`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Configuration in app config

You can configure `expo-navigation-bar` using its built-in [config plugin](/config-plugins/introduction) if you use config plugins in your project ([Continuous Native Generation (CNG)](/workflow/continuous-native-generation)). The plugin allows you to configure various properties that cannot be set at runtime and require building a new app binary to take effect. If your app does not use CNG, then you'll need to manually configure the library.

### Example app.json with config plugin

app.json

Copy


    {
      "expo": {
        "plugins": [
          [
            "expo-navigation-bar",
            {
              "enforceContrast": true,
              "hidden": false,
              "style": "light"
            }
          ]
        ]
      }
    }


### Configurable properties

Name| Default| Description
---|---|---
`enforceContrast`| `true`| Only for: Android
Determines whether the operating system should keep the navigation bar translucent to provide contrast between the navigation buttons and app content. Has no effect on Android 9 and below.
`hidden`| `undefined`| Only for: Android
Determines whether the status bar starts hidden. Accepts `true` and `false` as values.
`style`| `undefined`| Only for: Android
Determines which style the navigation bar starts with. Accepts `light` and `dark` as values.

Are you using this library in an existing React Native app?

If you're not using Continuous Native Generation ([CNG](/workflow/continuous-native-generation)) or you're using a native android project manually, then you need to add the following configuration to your native project:

  * To hide the navigation bar on Android, add `expoNavigationBarHidden` to android/app/src/main/res/values/styles.xml:

        <style name="AppTheme" parent="Theme.AppCompat.DayNight.NoActionBar">
          <!-- ... -->
          <item name="expoNavigationBarHidden">true</item>
        </style>



## Usage

Example

Copy

Open in Snack


    import { StyleSheet, Text, View } from 'react-native';
    import { NavigationBar } from 'expo-navigation-bar';

    export default function App() {
      return (
        <View style={styles.container}>
          <Text style={styles.text}>Notice that the navigation bar has light buttons!</Text>
          <NavigationBar style="light" />
        </View>
      );
    }

    const styles = StyleSheet.create({
      container: {
        flex: 1,
        backgroundColor: '#000',
        alignItems: 'center',
        justifyContent: 'center',
      },
      text: {
        color: '#fff',
      },
    });


## API


    import { NavigationBar } from 'expo-navigation-bar';


## Component

### `NavigationBar`

Android

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<NavigationBarProps>`

A component that allows you to configure your navigation bar declaratively.

You will likely have multiple `NavigationBar` components mounted in the same app at the same time. For example, if you have multiple screens in your app, you may end up using one per screen. The props of each `NavigationBar` component will be merged in the order that they were mounted.

NavigationBarProps

### `hidden`

Android

Optional • Type: `boolean`

If the navigation bar is hidden.

### `style`

Android

Optional • Type: `NavigationBarStyle` • Default: `'auto'`

Sets the color of the navigation bar buttons. Default value is `"auto"` which picks the appropriate value according to the active color scheme, eg: if your app is dark mode, the style will be `"light"`.

> This will have an effect when the following conditions are met:
>
>   * The device navigation bar is using buttons.
>   * The `enforceContrast` option of the `expo-navigation-bar` plugin is set to `false`.
>


> Due to a bug in the Android 15 emulator this function may have no effect. Try a physical device or an emulator with a different version of Android.

## Component Methods

### `setHidden(hidden)`

Android

Parameter| Type| Description
---|---|---
hidden| `boolean`| If the navigation bar should be hidden.




Set the navigation bar's visibility.

Returns:

`void`

Example


    NavigationBar.setHidden(true);


### `setStyle(style)`

Android

Parameter| Type| Description
---|---|---
style| `NavigationBarStyle`| The color of the navigation bar buttons.




Sets the style of the navigation bar.

> This will have an effect when the following conditions are met:
>
>   * The device navigation bar is using buttons.
>   * The `enforceContrast` option of the `expo-navigation-bar` plugin is set to `false`.
>


> Due to a bug in the Android 15 emulator this function may have no effect. Try a physical device or an emulator with a different version of Android.

Returns:

`void`

Example


    NavigationBar.setStyle("dark");


## Hooks

> Deprecated: This will be removed in a future release.

### `useVisibility()`

Android

React hook that statefully updates with the visibility of the system navigation bar.

Returns:

`NavigationBarVisibility | null`

Visibility of the navigation bar, `null` during async initialization.

## Methods

> Deprecated: This will be removed in a future release.

### `NavigationBar.getVisibilityAsync()`

Android

Get the navigation bar's visibility.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<NavigationBarVisibility>`

Navigation bar's current visibility status. Returns `hidden` on unsupported platforms (iOS, web).

> Deprecated: Use `NavigationBar.setStyle` instead. This will be removed in a future release.

### `NavigationBar.setStyle(style)`

Android

Parameter| Type| Description
---|---|---
style| `NavigationBarStyle`| The color of the navigation bar buttons.




Returns:

`void`

> Deprecated: Use `NavigationBar.setHidden` instead. This will be removed in a future release.

### `NavigationBar.setVisibilityAsync(visibility)`

Android

Parameter| Type| Description
---|---|---
visibility| `NavigationBarVisibility`| Based on CSS visibility property.




Set the navigation bar's visibility.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

## Event Subscriptions

> Deprecated: This will be removed in a future release.

### `NavigationBar.addVisibilityListener(listener)`

Android

Parameter| Type
---|---
listener| `(event: NavigationBarVisibilityEvent) => void`




Observe changes to the system navigation bar. Due to platform constraints, this callback will also be triggered when the status bar visibility changes.

Returns:

`EventSubscription`

## Types

### `NavigationBarStyle`

Android

Literal Type: `string`

Navigation bar style.

  * `auto` will automatically adjust based on the current theme.
  * `light` a light navigation bar with dark content.
  * `dark` a dark navigation bar with light content.
  * `inverted` the bar colors are inverted in relation to the current theme.


Acceptable values are: `'auto'` | `'inverted'` | `'light'` | `'dark'`

> Deprecated: This will be removed in a future release.

### `NavigationBarVisibility`

Android

Literal Type: `string`

Visibility of the navigation bar.

Acceptable values are: `'visible'` | `'hidden'`

> Deprecated: This will be removed in a future release.

### `NavigationBarVisibilityEvent`

Android

Current system UI visibility state. Due to platform constraints, this will return when the status bar visibility changes as well as the navigation bar.

Property| Type| Description
---|---|---
rawVisibility| `number`| Native Android system UI visibility state, returned from the native Android `setOnSystemUiVisibilityChangeListener` API.
visibility| `NavigationBarVisibility`| Current navigation bar visibility.