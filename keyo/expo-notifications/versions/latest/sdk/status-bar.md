---
title: "ExpoStatusBar"
url: https://docs.expo.dev/versions/latest/sdk/status-bar
---

# ExpoStatusBar

# Expo StatusBar

A library that provides the same interface as the React Native StatusBar API, but with slightly different defaults to work great in Expo environments.

Android

iOS

tvOS

Web

Included in Expo Go

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-status-bar)[npm](https://www.npmjs.com/package/expo-status-bar)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-status-bar/CHANGELOG.md)

Bundled version:

~56.0.4

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`expo-status-bar` gives you a component and imperative interface to control the app status bar to change its text color, hide it, and apply animations to any of these changes. Exactly what you are able to do with the `StatusBar` component depends on the platform you're using.

> tvOS and web support
>
> For tvOS, the `expo-status-bar` code will compile and run, but no status bar will show.
>
> For web, there is no API available to control the operating system's status bar, so `expo-status-bar` will do nothing and won't throw an error.

## Installation

Terminal

Copy

`- ``npx expo install expo-status-bar`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Configuration in app config

You can configure `expo-status-bar` using its built-in [config plugin](/config-plugins/introduction) if you use config plugins in your project ([Continuous Native Generation (CNG)](/workflow/continuous-native-generation)). The plugin allows you to configure various properties that cannot be set at runtime and require building a new app binary to take effect. If your app does not use CNG, then you'll need to manually configure the library.

### Example app.json with config plugin

app.json

Copy


    {
      "expo": {
        "plugins": [
          [
            "expo-status-bar",
            {
              "hidden": false,
              "style": "dark"
            }
          ]
        ]
      }
    }


### Configurable properties

Name| Default| Description
---|---|---
`hidden`| `undefined`| Determines whether the status bar starts hidden. Accepts `true` and `false` as values.
`style`| `undefined`| Determines which style the status bar starts with. Accepts `light` and `dark` as values.

Are you using this library in an existing React Native app?

If you're not using Continuous Native Generation ([CNG](/workflow/continuous-native-generation)) or you're using a native project manually, then you need to add the following configuration to your native project:

  * To hide the status bar on Android, add `expoStatusBarHidden` to android/app/src/main/res/values/styles.xml:

        <style name="AppTheme" parent="Theme.AppCompat.DayNight.NoActionBar">
          <!-- ... -->
          <item name="expoStatusBarHidden">true</item>
        </style>


  * To hide the status bar on iOS, set the following keys in your ios/<project>/Info.plist:

        <key>UIStatusBarHidden</key>
        <true/>



## Usage

Example

Copy

Open in Snack


    import { StyleSheet, Text, View } from 'react-native';
    import { StatusBar } from 'expo-status-bar';

    export default function App() {
      return (
        <View style={styles.container}>
          <Text style={styles.text}>Notice that the status bar has light text!</Text>
          <StatusBar style="light" />
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


    import { StatusBar } from 'expo-status-bar';


## Component

### `StatusBar`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<StatusBarProps>`

A component that allows you to configure your status bar declaratively.

You will likely have multiple `StatusBar` components mounted in the same app at the same time. For example, if you have multiple screens in your app, you may end up using one per screen. The props of each `StatusBar` component will be merged in the order that they were mounted. This component is built on top of the [StatusBar](https://reactnative.dev/docs/statusbar) component exported from React Native, and it provides defaults that work better for Expo users.

StatusBarProps

### `animated`

Android

iOS

tvOS

Web

Optional • Type: `boolean`

If the transition between status bar property changes should be animated. Supported for `style` and `hidden`.

### `hidden`

Android

iOS

tvOS

Web

Optional • Type: `boolean`

If the status bar is hidden.

### `hideTransitionAnimation`

iOS

Optional • Type: `StatusBarAnimation` • Default: `'fade'`

The transition effect when showing and hiding the status bar using the hidden prop.

### `style`

Android

iOS

tvOS

Web

Optional • Type: `StatusBarStyle` • Default: `'auto'`

Sets the color of the status bar text. Default value is `"auto"` which picks the appropriate value according to the active color scheme, eg: if your app is dark mode, the style will be `"light"`.

## Component Methods

### `setHidden(hidden, animation)`

Android

iOS

tvOS

Web

Parameter| Type| Description
---|---|---
hidden| `boolean`| If the status bar should be hidden.
animation(optional)| `StatusBarAnimation`| Animation to use when toggling hidden, defaults to `'none'`.




Toggle visibility of the status bar.

Returns:

`void`

Example


    StatusBar.setHidden(true, 'slide');


### `setStyle(style, animated)`

Android

iOS

tvOS

Web

Parameter| Type| Description
---|---|---
style| `StatusBarStyle`| The color of the status bar text.
animated(optional)| `boolean`| If the transition should be animated.




Set the bar style of the status bar.

Returns:

`void`

Example


    StatusBar.setStyle('dark', true);


## Methods

> Deprecated: Use `StatusBar.setHidden` instead. This will be removed in a future release.

### `StatusBar.setStatusBarHidden(hidden, animation)`

Android

iOS

tvOS

Web

Parameter| Type| Description
---|---|---
hidden| `boolean`| If the status bar should be hidden.
animation(optional)| `StatusBarAnimation`| Animation to use when toggling hidden, defaults to `'none'`.




Returns:

`void`

> Deprecated: Use `StatusBar.setStyle` instead. This will be removed in a future release.

### `StatusBar.setStatusBarStyle(style, animated)`

Android

iOS

tvOS

Web

Parameter| Type| Description
---|---|---
style| `StatusBarStyle`| The color of the status bar text.
animated(optional)| `boolean`| If the transition should be animated.




Returns:

`void`

## Types

### `StatusBarAnimation`

Android

iOS

tvOS

Web

Literal Type: `string`

Acceptable values are: `'none'` | `'fade'` | `'slide'`

### `StatusBarStyle`

Android

iOS

tvOS

Web

Literal Type: `string`

Acceptable values are: `'auto'` | `'inverted'` | `'light'` | `'dark'`