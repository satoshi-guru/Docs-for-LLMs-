---
title: "react-native-pager-view"
url: https://docs.expo.dev/versions/latest/sdk/view-pager
---

# react-native-pager-view

# react-native-pager-view

A component library that provides a carousel-like view to swipe through pages of content.

Android

iOS

Included in Expo Go

Ask AI

[GitHub](https://github.com/callstack/react-native-pager-view)[npm](https://www.npmjs.com/package/react-native-pager-view)

Bundled version:

8.0.1

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

> [`@expo/ui` provides a drop-in replacement](/versions/latest/sdk/ui/drop-in-replacements/pagerview) for `react-native-pager-view`, powered by Jetpack Compose on Android and SwiftUI on iOS.

`react-native-pager-view` exposes a component that provides the layout and gestures to scroll between pages of content, like a carousel.

## Installation

Terminal

Copy

`- ``npx expo install react-native-pager-view`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project. Then, follow the [installation instructions](https://github.com/callstack/react-native-pager-view#linking) provided in the library's README or documentation.

## Example

App.js

Copy


    import { StyleSheet, View, Text } from 'react-native';
    import PagerView from 'react-native-pager-view';

    export default function MyPager() {
      return (
        <View style={styles.container}>
          <PagerView style={styles.container} initialPage={0}>
            <View style={styles.page} key="1">
              <Text>First page</Text>
              <Text>Swipe ➡️</Text>
            </View>
            <View style={styles.page} key="2">
              <Text>Second page</Text>
            </View>
            <View style={styles.page} key="3">
              <Text>Third page</Text>
            </View>
          </PagerView>
        </View>
      );
    }

    const styles = StyleSheet.create({
      container: {
        flex: 1,
      },
      page: {
        justifyContent: 'center',
        alignItems: 'center',
      },
    });


## Learn more

[Visit official documentationGet full information on API and its usage.](https://github.com/callstack/react-native-pager-view)