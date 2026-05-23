---
title: "react-native-keyboard-controller"
url: https://docs.expo.dev/versions/latest/sdk/keyboard-controller
---

# react-native-keyboard-controller

# react-native-keyboard-controller

A library that provides a Keyboard manager that works in an identical way on Android and iOS

Android

iOS

Included in Expo Go

Ask AI

[GitHub](https://github.com/kirillzyusko/react-native-keyboard-controller)[npm](https://www.npmjs.com/package/react-native-keyboard-controller)

Bundled version:

1.21.6

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`react-native-keyboard-controller` offers additional functionality beyond the built-in React Native keyboard APIs, providing consistency across Android and iOS with minimal configuration and offering the native feel users expect.

## Installation

Terminal

Copy

`- ``npx expo install react-native-keyboard-controller`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project. Then, follow the [installation instructions](https://kirillzyusko.github.io/react-native-keyboard-controller/docs/installation) provided in the library's README or documentation.

## Usage

KeyboardController

Copy

Open in Snack


    import { TextInput, View, StyleSheet } from 'react-native';
    import { KeyboardAwareScrollView, KeyboardToolbar } from 'react-native-keyboard-controller';

    export default function FormScreen() {
      return (
        <>
          <KeyboardAwareScrollView bottomOffset={62} contentContainerStyle={styles.container}>
            <View>
              <TextInput placeholder="Type a message..." style={styles.textInput} />
              <TextInput placeholder="Type a message..." style={styles.textInput} />
            </View>
            <TextInput placeholder="Type a message..." style={styles.textInput} />
            <View>
              <TextInput placeholder="Type a message..." style={styles.textInput} />
              <TextInput placeholder="Type a message..." style={styles.textInput} />
              <TextInput placeholder="Type a message..." style={styles.textInput} />
            </View>
            <TextInput placeholder="Type a message..." style={styles.textInput} />
          </KeyboardAwareScrollView>
          <KeyboardToolbar />
        </>
      );
    }

    const styles = StyleSheet.create({
      container: {
        gap: 16,
        padding: 16,
      },
      listStyle: {
        padding: 16,
        gap: 16,
      },
      textInput: {
        width: 'auto',
        flexGrow: 1,
        flexShrink: 1,
        height: 45,
        borderWidth: 1,
        borderRadius: 8,
        borderColor: '#d8d8d8',
        backgroundColor: '#fff',
        padding: 8,
        marginBottom: 8,
      },
    });


## Additional resources

[Advanced keyboard handlingLearn more about advanced keyboard handling examples with Keyboard Controller.](/guides/keyboard-handling#advanced-keyboard-handling-with-keyboard-controller) [Visit official documentationGet full information on API and its usage.](https://kirillzyusko.github.io/react-native-keyboard-controller/)