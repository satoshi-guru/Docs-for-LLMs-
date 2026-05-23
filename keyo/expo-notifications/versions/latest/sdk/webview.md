---
title: "react-native-webview"
url: https://docs.expo.dev/versions/latest/sdk/webview
---

# react-native-webview

# react-native-webview

A library that provides a WebView component.

Android

iOS

Included in Expo Go

Ask AI

[GitHub](https://github.com/react-native-webview/react-native-webview)[npm](https://www.npmjs.com/package/react-native-webview)

Bundled version:

13.16.1

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`react-native-webview` provides a `WebView` component that renders web content in a native view.

## Installation

Terminal

Copy

`- ``npx expo install react-native-webview`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project. Then, follow the [installation instructions](https://github.com/react-native-webview/react-native-webview/blob/master/docs/Getting-Started.md#react-native-webview-getting-started-guide) provided in the library's README or documentation.

## Usage

Basic Webview usage

Copy

Open in Snack


    import { WebView } from 'react-native-webview';
    import Constants from 'expo-constants';
    import { StyleSheet } from 'react-native';

    export default function App() {
      return (
        <WebView
          style={styles.container}
          source={{ uri: 'https://expo.dev' }}
        />
      );
    }

    const styles = StyleSheet.create({
      container: {
        flex: 1,
        marginTop: Constants.statusBarHeight,
      },
    });


### With inline HTML

Webview inline HTML

Copy

Open in Snack


    import { WebView } from 'react-native-webview';
    import Constants from 'expo-constants';
    import { StyleSheet } from 'react-native';

    export default function App() {
      return (
        <WebView
          style={styles.container}
          originWhitelist={['*']}
          source={{ html: '<h1><center>Hello world</center></h1>' }}
        />
      );
    }

    const styles = StyleSheet.create({
      container: {
        flex: 1,
        marginTop: Constants.statusBarHeight,
      },
    });


## Learn more

[Visit official documentationGet full information on API and its usage.](https://github.com/react-native-webview/react-native-webview/blob/master/docs/Guide.md)