---
title: "ExpoSystemUI"
url: https://docs.expo.dev/versions/latest/sdk/system-ui
---

# ExpoSystemUI

# Expo SystemUI

A library that allows interacting with system UI elements.

Android

iOS

tvOS

Web

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-system-ui)[npm](https://www.npmjs.com/package/expo-system-ui)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-system-ui/CHANGELOG.md)

Bundled version:

~56.0.5

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`expo-system-ui` enables you to interact with UI elements that fall outside of the React tree. Specifically the root view background color, and locking the user interface style globally on Android.

## Installation

Terminal

Copy

`- ``npx expo install expo-system-ui`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Configuration in app config

You can configure `expo-system-ui` using its built-in [config plugin](/config-plugins/introduction) if you use config plugins in your project ([Continuous Native Generation (CNG)](/workflow/continuous-native-generation)). The plugin allows you to configure [`userInterfaceStyle`](/versions/latest/config/app#userinterfacestyle) on Android and [`backgroundColor`](/versions/latest/config/app#backgroundcolor) on iOS properties from [app config](/versions/latest/config/app). These properties cannot be set at runtime and require building a new app binary to take effect. If your app does not use CNG, then you'll need to manually configure the library.

### Example app.json with config plugin

app.json

Copy


    {
      "expo": {
        "backgroundColor": "#ffffff",
        "userInterfaceStyle": "light",
        "ios": {
          "backgroundColor": "#ffffff",
        }
        "android": {
          "userInterfaceStyle": "light"
        },
        "plugins": ["expo-system-ui"],
      }
    }


Are you using this library in an existing React Native app?

If you're not using Continuous Native Generation ([CNG](/workflow/continuous-native-generation)) or you're using native android and ios project manually, then you need to add the following configuration to your native project:

Android

To apply `userInterfaceStyle` on Android, you need to add the `expo_system_ui_user_interface_style` configuration android/app/src/main/res/values/strings.xml:


    <resources>
      <!-- ... -->
      <string name="expo_system_ui_user_interface_style" translatable="false">light</string> <!-- or dark -->
    </resources>


iOS

To apply `backgroundColor` on iOS, you need to add the `UIUserInterfaceStyle` configuration in ios/your-app/Info.plist:


    <plist>
      <dict>
        <!-- ... -->
        <key>UIUserInterfaceStyle</key>
        <string>Light</string> <!-- or Dark -->
      </dict>
    </plist>


## API


    import * as SystemUI from 'expo-system-ui';


## Methods

### `SystemUI.getBackgroundColorAsync()`

Android

iOS

tvOS

Web

Gets the root view background color.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<[ColorValue](https://reactnative.dev/docs/colors) | null>`

Current root view background color in hex format. Returns `null` if the background color is not set.

Example


    const color = await SystemUI.getBackgroundColorAsync();


### `SystemUI.setBackgroundColorAsync(color)`

Android

iOS

tvOS

Web

Parameter| Type| Description
---|---|---
color| `[ColorValue](https://reactnative.dev/docs/colors) | null`| Any valid [CSS 3 (SVG) color](http://www.w3.org/TR/css3-color/#svg-color).




Changes the root view background color. Call this function in the root file outside of your component.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    SystemUI.setBackgroundColorAsync("black");