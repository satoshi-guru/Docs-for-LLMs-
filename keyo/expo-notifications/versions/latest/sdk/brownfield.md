---
title: "ExpoBrownfield"
url: https://docs.expo.dev/versions/latest/sdk/brownfield
---

# ExpoBrownfield

# Expo Brownfield

Toolkit and APIs for integrating Expo into existing native applications.

Android

iOS

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-brownfield)[npm](https://www.npmjs.com/package/expo-brownfield)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-brownfield/CHANGELOG.md)

Bundled version:

~56.0.13

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`expo-brownfield` is a toolkit for adding React Native views to existing native Android and iOS applications. It provides:

  * Built-in APIs for bi-directional communication and navigation between native and React Native apps
  * Config plugin for automatic setup of brownfield targets in your Expo project
  * CLI for building and publishing artifacts to Maven repositories (Android) and XCFrameworks (iOS)


## Installation

Terminal

Copy

`- ``npx expo install expo-brownfield`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Usage

### Communication API

The Communication API enables bi-directional, message-based communication between the native (host) app and React Native.

#### Sending messages from React Native to native


    import * as Brownfield from 'expo-brownfield';

    Brownfield.sendMessage({
      type: 'MyMessage',
      data: {
        language: 'TypeScript',
        expo: true,
        platforms: ['android', 'ios'],
      },
    });


#### Receiving messages from native in React Native


    import * as Brownfield, { type MessageEvent } from 'expo-brownfield';
    import { useEffect } from 'react';

    function MyComponent() {
      useEffect(() => {
        const handleMessage = (event: MessageEvent) => {
          console.log('Received message:', event);
        };

        Brownfield.addMessageListener(handleMessage);

        return () => {
          Brownfield.removeMessageListener(handleMessage);
        };
      }, []);

      // ...
    }


#### Sending messages from native to React Native

Android

iOS


    import expo.modules.brownfield.BrownfieldMessaging

    BrownfieldMessaging.sendMessage(mapOf(
        "type" to "MyAndroidMessage",
        "timestamp" to System.currentTimeMillis(),
        "data" to mapOf(
            "platform" to "android"
        )
    ))



    import ExpoBrownfield

    BrownfieldMessaging.sendMessage([
        "type": "MyIOSMessage",
        "timestamp": Date().timeIntervalSince1970,
        "data": [
            "platform": "ios"
        ]
    ])


#### Receiving messages from React Native in native

Android

iOS


    import expo.modules.brownfield.BrownfieldMessaging

    val listenerId = BrownfieldMessaging.addListener { event ->
        println("Message from React Native: $event")
    }

    // Later, to remove the listener:
    BrownfieldMessaging.removeListener(listenerId)



    import ExpoBrownfield

    let listenerId = BrownfieldMessaging.addListener { message in
        print("Message from React Native: \(message)")
    }

    // Later, to remove the listener:
    BrownfieldMessaging.removeListener(id: listenerId)


## Configuration in app config

The `expo-brownfield` package provides a [config plugin](/config-plugins/introduction) that can be used to configure the brownfield integration when using [Continuous Native Generation (CNG)](/workflow/continuous-native-generation). This plugin allows you to customize how your Expo project is packaged and integrated into your existing native app.

### Example app.json with config plugin

app.json

Copy


    {
      "expo": {
        "plugins": [
          [
            "expo-brownfield",
            {
              "ios": {
                "targetName": "MyBrownfieldTarget",
                "bundleIdentifier": "com.example.brownfield"
              },
              "android": {
                "group": "com.example",
                "libraryName": "brownfield",
                "package": "com.example.brownfield",
                "version": "1.0.0"
              }
            }
          ]
        ]
      }
    }


### Configurable properties

Name| Default| Description
---|---|---
`ios.targetName`| `"<scheme>brownfield" or "<slug>brownfield"`| Only for: iOS
Name of the Xcode target for the brownfield integration. This is used to create a separate target in your Xcode project for the React Native code.
`ios.bundleIdentifier`| `"<ios.bundleIdentifier base>.<targetName>" or "com.example.<targetName>"`| Only for: iOS
Bundle identifier for the brownfield target. This should be unique and different from your main app bundle identifier.
`ios.buildReactNativeFromSource`| `false`| Only for: iOS
Build React Native from source instead of using prebuilt frameworks. Turning this on significantly increases the build times.
`android.group`| `"<package without last segment>"`| Only for: Android
Maven group ID for the generated Android library. This is used when publishing the library to a Maven repository.
`android.libraryName`| `"brownfield"`| Only for: Android
Name of the generated Android library module.
`android.package`| `"<android.package>.brownfield" or "com.example.brownfield"`| Only for: Android
Java/Kotlin package name for the generated Android library code.
`android.version`| `"1.0.0"`| Only for: Android
Version string for the generated Android library. This is used when publishing to a Maven repository.
`android.publishing`| `[{ type: "localMaven" }]`| Only for: Android
Publishing configuration for the generated Android library. Supports `localMaven`, `localDirectory`, `remotePublic`, and `remotePrivate` publication types. Each type has different configuration options for specifying where and how the library is published.

## CLI

The `expo-brownfield` library includes a CLI for building and publishing to Maven repositories (Android) and XCFrameworks (iOS).

Terminal

Copy

`- ``npx expo-brownfield [command] [options]`

### Commands

#### `build:android`

Builds and publishes the brownfield library and its dependencies to Maven repositories.

Terminal

Copy

`- ``npx expo-brownfield build:android [options]`

Option| Description
---|---
`-d, --debug`| Build in debug mode
`-r, --release`| Build in release mode
`-a, --all`| Build in both debug and release mode (default)
`-l, --library`| Specify brownfield library name
`--repo, --repository`| Specify Maven repositories to publish to
`-t, --task`| Specify Gradle publish tasks to run
`--verbose`| Include all logs from subprocesses

#### `build:ios`

Builds the brownfield XCFramework and copies the Hermes XCFramework to the artifacts directory.

Terminal

Copy

`- ``npx expo-brownfield build:ios [options]`

Option| Description
---|---
`-d, --debug`| Build in debug mode
`-r, --release`| Build in release mode (default)
`-a, --artifacts`| Path to the artifacts directory (default: `./artifacts`)
`-s, --scheme`| Xcode scheme to build
`-x, --xcworkspace`| Xcode workspace path
`-p, --package`| Ship artifacts as Swift Package (with optional name)
`--verbose`| Include all logs from subprocesses

#### `tasks:android`

Lists all available publish tasks and Maven repositories.

Terminal

Copy

`- ``npx expo-brownfield tasks:android`

## API


    import * as Brownfield from 'expo-brownfield';


## Hooks

### `useSharedState(key, initialValue)`

Android

iOS

Parameter| Type| Description
---|---|---
key| `string`| The key to get the value for.
initialValue(optional)| `T`| The initial value to be used if the shared state is not set.




Hook to observe and set the value of shared state for a given key. Provides a synchronous API similar to `useState`.

Returns:

`[T | undefined, (value: T | (prev: T | undefined) => T) => void]`

A tuple containing the value and a function to set the value.

## Methods

### `Brownfield.deleteSharedState(key)`

Android

iOS

Parameter| Type| Description
---|---|---
key| `string`| The key to delete the shared state for.




Deletes the shared state for a given key.

Returns:

`void`

### `Brownfield.getMessageListenerCount()`

Android

iOS

Gets the number of registered message listeners.

Returns:

`number`

The number of active message listeners.

### `Brownfield.getSharedStateValue(key)`

Android

iOS

Parameter| Type| Description
---|---|---
key| `string`| The key to get the value for.




Gets the value of shared state for a given key.

Returns:

`T | undefined`

### `Brownfield.popToNative(animated)`

Android

iOS

Parameter| Type| Description
---|---|---
animated(optional)| `boolean`| Whether to animate the transition (iOS only). Defaults to `false`.Default:`false`




Navigates back to the native part of the app, dismissing the React Native view.

Returns:

`void`

### `Brownfield.sendMessage(message)`

Android

iOS

Parameter| Type| Description
---|---|---
message| `Record<string, any>`| A dictionary containing the message payload to send to native.




Sends a message to the native side of the app. The message can be received by setting up a listener in the native code.

Returns:

`void`

### `Brownfield.setNativeBackEnabled(enabled)`

Android

iOS

Parameter| Type| Description
---|---|---
enabled| `boolean`| Whether to enable native back button handling.




Enables or disables the native back button behavior. When enabled, pressing the back button will navigate back to the native part of the app instead of performing the default React Navigation back action.

Returns:

`void`

### `Brownfield.setSharedStateValue(key, value)`

Android

iOS

Parameter| Type| Description
---|---|---
key| `string`| The key to set the value for.
value| `T`| The value to be set.




Sets the value of shared state for a given key.

Returns:

`void`

## Event Subscriptions

### `Brownfield.addMessageListener(listener)`

Android

iOS

Parameter| Type| Description
---|---|---
listener| `Listener<[MessageEvent](https://developer.mozilla.org/en-US/docs/Web/API/MessageEvent)>`| A callback function that receives message events from native.




Adds a listener for messages sent from the native side of the app.

Returns:

`EventSubscription`

A subscription object that can be used to remove the listener.

Example


    const subscription = addMessageListener((event) => {
      console.log('Received message from native:', event);
    });

    // Later, to remove the listener:
    subscription.remove();


### `Brownfield.addSharedStateListener(key, callback)`

Android

iOS

Parameter| Type| Description
---|---|---
key| `string`| The key to add the listener for.
callback| `(value: T | undefined) => void`| The callback to be called when the shared state changes.




Adds a listener for changes to the shared state for a given key.

Returns:

`EventSubscription`

A subscription object that can be used to remove the listener.

### `Brownfield.removeAllMessageListeners()`

Android

iOS

Removes all message listeners.

Returns:

`void`

### `Brownfield.removeMessageListener(listener)`

Android

iOS

Parameter| Type| Description
---|---|---
listener| `Listener<[MessageEvent](https://developer.mozilla.org/en-US/docs/Web/API/MessageEvent)>`| The listener function to remove.




Removes a specific message listener.

Returns:

`void`

## Interfaces

### `EventSubscription`

Android

iOS

A subscription object that allows to conveniently remove an event listener from the emitter.

EventSubscription Methods

### `remove()`

Android

iOS

Removes an event listener for which the subscription has been created. After calling this function, the listener will no longer receive any events from the emitter.

Returns:

`void`

## Types

### `MessageEvent`

Android

iOS

Type: `Record<string, any>`