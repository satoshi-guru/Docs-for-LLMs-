---
title: "react-native-reanimated"
url: https://docs.expo.dev/versions/latest/sdk/reanimated
---

# react-native-reanimated

# react-native-reanimated

A library that provides an API that greatly simplifies the process of creating smooth, powerful, and maintainable animations.

Android

iOS

tvOS

Web

Included in Expo Go

Ask AI

[GitHub](https://github.com/software-mansion/react-native-reanimated)[npm](https://www.npmjs.com/package/react-native-reanimated)

Bundled version:

4.3.1

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

[`react-native-reanimated`](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/getting-started/) provides an API that greatly simplifies the process of creating smooth, powerful, and maintainable animations.

> Reanimated uses React Native APIs that are incompatible with "Remote JS Debugging" for JavaScriptCore. To use a debugger with your app with `react-native-reanimated`, you'll need to use the [Hermes JavaScript engine](/guides/using-hermes) and the [JavaScript Inspector for Hermes](/guides/using-hermes#javascript-inspector-for-hermes).

## Installation

Terminal

Copy

`- ``npx expo install react-native-reanimated react-native-worklets`




No additional configuration is required. [Reanimated Babel plugin](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/glossary#reanimated-babel-plugin) is automatically configured in [`babel-preset-expo`](https://www.npmjs.com/package/babel-preset-expo) when you install the library.

## Usage

The following example shows how to use the `react-native-reanimated` library to create a simple animation.

Using react-native-reanimated

Copy

Open in Snack


    import Animated, {
      useSharedValue,
      withTiming,
      useAnimatedStyle,
      Easing,
    } from 'react-native-reanimated';
    import { View, Button, StyleSheet } from 'react-native';

    export default function AnimatedStyleUpdateExample() {
      const randomWidth = useSharedValue(10);

      const config = {
        duration: 500,
        easing: Easing.bezier(0.5, 0.01, 0, 1),
      };

      const style = useAnimatedStyle(() => {
        return {
          width: withTiming(randomWidth.value, config),
        };
      });

      return (
        <View style={styles.container}>
          <Animated.View style={[styles.box, style]} />
          <Button
            title="toggle"
            onPress={() => {
              randomWidth.value = Math.random() * 350;
            }}
          />
        </View>
      );
    }

    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
      },
      box: {
        width: 100,
        height: 80,
        backgroundColor: 'black',
        margin: 30,
      },
    });


## Learn more

[Visit official documentationGet full information on API and its usage.](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/your-first-animation)