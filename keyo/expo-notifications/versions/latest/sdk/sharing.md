---
title: "ExpoSharing"
url: https://docs.expo.dev/versions/latest/sdk/sharing
---

# ExpoSharing

# Expo Sharing

A library that provides functionality for sharing and receiving data from other apps.

Android

iOS

Web

Included in Expo Go

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-sharing)[npm](https://www.npmjs.com/package/expo-sharing)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-sharing/CHANGELOG.md)

Bundled version:

~56.0.12

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`expo-sharing` allows you to share files directly with other compatible applications and to receive compatible data shared from other apps.

#### Sharing limitations on web

  * `expo-sharing` for web is built on top of the Web Share API, which still has [very limited browser support](https://caniuse.com/#feat=web-share). Be sure to check that the API can be used before calling it by using `Sharing.isAvailableAsync()`.
  * HTTPS required on web: The Web Share API is only available on web when the page is served over https. Run your app with `npx expo start --tunnel` to enable it.
  * No local file sharing on web: Sharing local files by URI works on Android and iOS, but not on web. You cannot share local files on web by URI — you will need to upload them somewhere and share that URI.


## Installation

Terminal

Copy

`- ``npx expo install expo-sharing`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Configuration in app config

You can configure `expo-sharing` using its built-in [config plugin](/config-plugins/introduction) if you use config plugins in your project ([Continuous Native Generation (CNG)](/workflow/continuous-native-generation)). The plugin allows you to configure various properties that cannot be set at runtime and require building a new app binary to take effect. If your app does not use CNG, then you'll need to manually configure the library.

### Example app.json with config plugin

The following example shows a configuration that allows sharing a single or multiple images on Android and iOS:

app.json

Copy


    {
      "expo": {
        "plugins": [
          [
            "expo-sharing",
            {
              "ios": {
                "enabled": true,
                "activationRule": {
                  "supportsImageWithMaxCount": 5
                }
              },
              "android": {
                "enabled": true,
                "singleShareMimeTypes": ["image/*"],
                "multipleShareMimeTypes": ["image/*"]
              }
            }
          ]
        ]
      }
    }


### Configurable properties

Name| Default| Description
---|---|---
`ios.enabled`| `false`| A boolean value to enable the iOS Share Extension. If `true`, a share extension target is added to the project.
`ios.extensionBundleIdentifier`| `{appBundleIdentifier}.ShareExtension`| The bundle identifier for the iOS Share Extension.
`ios.appGroupId`| `group.{appBundleIdentifier}`| The App Group ID to use for sharing data between the app and the extension.
`ios.activationRule`| `{}`| Configuration for the `NSExtensionActivationRule` in Info.plist. Can be either an object confirming to the `ActivationRuleOptions` type to generate a standard predicate or a raw string to specify a custom predicate directly (for example, `SUBQUERY(...)`).
`android.enabled`| `false`| A boolean value to enable Android share intent handling. If `true`, adds the necessary `intent-filter` to the AndroidManifest.xml.
`android.singleShareMimeTypes`| `[]`| An array of MIME types to accept for single file sharing (using `ACTION_SEND` intent).
`android.multipleShareMimeTypes`| `[]`| An array of MIME types to accept for multiple file sharing (using `ACTION_SEND_MULTIPLE` intent).

## Sharing to your app from other apps

> Note: This functionality is currently [experimental](/more/release-statuses#experimental). On iOS the share extension opens the main target, instead of processing the share in a sharing `ViewController`, which is not officially supported by Apple and may stop working in a future iOS release.

When an app user shares content with your app, the operating system launches your app (also known as the process of bringing it to the foreground). To process this action, you need to configure your navigation to handle the incoming deep link.

### Expo Router

If you are using [Expo Router](/router/introduction), you can use the [+native-intent.ts](/router/advanced/native-intent) file to handle the incoming share intent. This allows you to inspect the incoming path and redirect to a specific route.

app/+native-intent.ts

Copy


    import { getSharedPayloads } from 'expo-sharing';

    export async function redirectSystemPath({ path, initial }: { path: string; initial: boolean }) {
      try {
        // Check if the URL is from the share extension/intent
        if (new URL(path).hostname === 'expo-sharing') {
          return '/handle-share';
        }
        return path;
      } catch {
        // Fallback to the root path on error
        return '/';
      }
    }


### React Navigation

If you are using [React Navigation](https://reactnavigation.org/), you can use the `linking` prop to intercept the deep link. You should check if the incoming URL hostname matches the `expo-sharing` scheme and redirect the user to a specific handler screen.


    import * as Linking from 'expo-linking';
    import { createStaticNavigation } from '@react-navigation/native';
    import { createNativeStackNavigator } from '@react-navigation/native-stack';

    const RootStack = createNativeStackNavigator({
      screens: {
        // Other screens
        HandleShare: {
          screen: HandleShare,
          linking: {
            path: '/handle-share',
          },
        },
      },
    });

    const Navigation = createStaticNavigation(RootStack);

    function processUrl(url: string | null) {
      if (!url) return null;

      // The path to your share handler screen
      const handlerUrl = Linking.createURL('/handle-share');

      // Check if the URL is from the share extension/intent
      if (new URL(url).hostname === 'expo-sharing') {
        return handlerUrl;
      }
      return url;
    }

    export default function App() {
      return (
        <Navigation
          // The rest of your navigation config
          linking={{
            prefixes: [Linking.createURL('/')],
            async getInitialURL() {
              const initialUrl = await Linking.getInitialURL();
              return processUrl(initialUrl);
            },
            subscribe(listener) {
              const linkingSubscription = Linking.addEventListener('url', ({ url }) => {
                const processedUrl = processUrl(url) ?? url;
                listener(processedUrl);
              });

              return () => {
                linkingSubscription.remove();
              };
            },
          }}
        />
      );
    }


### No navigation library

If you are creating a basic app without a navigation library, your main screen is the handler screen. You can move on to the next section.

## Displaying shared content

Once you have redirected the user to a handler screen, you can use the `useIncomingShare` hook to access and display the shared data.

The following example shows a screen that displays shared images:


    import { Image } from 'expo-image';
    import { useIncomingShare } from 'expo-sharing';
    import { View, StyleSheet, ActivityIndicator } from 'react-native';

    export default function ShareReceived() {
      const { resolvedSharedPayloads, isResolving } = useIncomingShare();

      if (isResolving) {
        return (
          <View style={styles.container}>
            <ActivityIndicator size="large" />
          </View>
        );
      }

      return (
        <View style={styles.container}>
          {resolvedSharedPayloads.map((payload, index) => {
            if (payload.contentType === 'image') {
              return <Image source={{ uri: payload.contentUri }} style={styles.image} key={index} />;
            }
            return null;
          })}
        </View>
      );
    }

    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        backgroundColor: 'white',
      },
      image: {
        width: 300,
        height: 300,
        marginBottom: 20,
        borderRadius: 10,
      },
    });


## API


    import * as Sharing from 'expo-sharing';


## Hooks

### `useIncomingShare()`

Android

iOS

Web

Hook, which returns the data shared with the application and updates the data if the shared payload has changed.

Returns:

`UseIncomingShareResult`

## Props

### `android`

Android

iOS

Web

Optional • Type: `{  enabled: boolean,  multipleShareMimeTypes: string[],  singleShareMimeTypes: string[] }`

### `ios`

Android

iOS

Web

Optional • Type: `{  activationRule: ActivationRule,  appGroupId: string,  enabled: boolean,  extensionBundleIdentifier: string }`

## Methods

### `Sharing.clearSharedPayloads()`

Android

iOS

Web

Clears the data shared with the app.

Returns:

`void`

### `Sharing.getResolvedSharedPayloadsAsync()`

Experimental

•

Android

iOS

Returns resolved data shared with the app. Compared to data returned from `getSharedPayloads` contains additional information useful for reading and displaying the data. For example, when a web `URL` is shared with the app, a resolved payload will contain additional information about the URL contents.

> Depending on what has been shared, this method may require a network connection to resolve content details.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ResolvedSharePayload[]>`

### `Sharing.getSharedPayloads()`

Experimental

•

Android

iOS

Returns raw data shared with the app. Returns an empty array if no data has been shared with the app.

Returns:

`SharePayload[]`

### `Sharing.isAvailableAsync()`

Android

iOS

Web

Determine if the sharing API can be used in this app.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

A promise that fulfills with `true` if the sharing API can be used, and `false` otherwise.

### `Sharing.shareAsync(url, options)`

Android

iOS

Web

Parameter| Type| Description
---|---|---
url| `string`| Local file URL to share.
options(optional)| `SharingOptions`| A map of share options.Default:`{}`




Opens action sheet to share file to different applications which can handle this type of file.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

## Types

### `BaseResolvedSharePayload`

Android

iOS

Web

Type: `SharePayload` extended by:

Property| Type| Description
---|---|---
contentMimeType| `string | null`| Mime type of the content accessible via the `contentUri`.
contentSize| `number | null`| Size of the content accessible via the `contentUri`.
contentType| `ContentType | null`| Type of the content accessible via the `contentUri`.
contentUri| `string | null`| URI which can be used to access the shared content. When resolving contents of a URL with redirects, contains the redirect target URI. Null when resolving a `SharePayload` with a `text` `ShareType`.
originalName| `string | null`| If applicable, value of the `suggestedFilename` HTTP header field, otherwise the last path component of the `contentUri` field.

### `ContentType`

Experimental

•

Android

iOS

Literal Type: `string`

Describes the resolved content type.

Acceptable values are: `'text'` | `'audio'` | `'image'` | `'video'` | `'file'` | `'website'`

### `ResolvedSharePayload`

Experimental

•

Android

iOS

Literal Type: `union`

Represents a payload shared with the app, with additional information about the shared contents.

Acceptable values are: `UriBasedResolvedSharePayload` | `TextBasedResolvedSharePayload`

### `SharePayload`

Experimental

•

Android

iOS

Represents raw data shared with the app.

Property| Type| Description
---|---|---
mimeType(optional)| `string`| The MIME type of the contents of the`value` field.Default:`'text/plain'`
shareType(optional)| `ShareType`| The type of the shared content.Default:`'text'`
value(optional)| `string`| The primary value of the content.

  * For `text`, this is the message body.
  * For `url`, this is the URL string.
  * For `file`, `image`, `video`, or `audio`, this is typically the file URI.

Default:`""`

### `ShareType`

Experimental

•

Android

iOS

Literal Type: `string`

Determines the type of content being shared.

  * `text`: Plain text content.
  * `url`: A specific URL.
  * `audio`: An audio file.
  * `image`: An image file.
  * `video`: A video file.
  * `file`: A generic file.


Acceptable values are: `'text'` | `'url'` | `'audio'` | `'image'` | `'video'` | `'file'`

### `SharingOptions`

Android

iOS

Web

Property| Type| Description
---|---|---
anchor(optional)| `{  height: number,  width: number,  x: number,  y: number }`| Only for: iOS
Sets the anchor point for iPad
dialogTitle(optional)| `string`| Only for: AndroidWeb
Sets share dialog title.
mimeType(optional)| `string`| Only for: Android
Sets `mimeType` for `Intent`.
UTI(optional)| `string`| Only for: iOS
[Uniform Type Identifier](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_conc/understand_utis_conc.html)

  * the type of the target file.



### `TextBasedResolvedSharePayload`

Experimental

•

Android

iOS

Represents a resolved payload, where a text was shared with the app.

Type: `BaseResolvedSharePayload` extended by:

Property| Type| Description
---|---|---
contentType(optional)| `'text'`| -

### `UriBasedResolvedSharePayload`

Experimental

•

Android

iOS

Represents a resolved payload, for which the data can be accessed through a URI.

Type: `BaseResolvedSharePayload` extended by:

Property| Type| Description
---|---|---
contentType| `'audio' | 'file' | 'video' | 'image' | 'website'`| -
contentUri| `string`| -

### `UseIncomingShareResult`

Experimental

•

Android

iOS

Object returned by `useIncomingShare` hook containing information about data shared with the app.

Property| Type| Description
---|---|---
clearSharedPayloads| `() => void`| Clears payloads shared with the app.
error| `[Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error) | null`| Contains an error encountered while resolving the shared payload. Null on success.
isResolving| `boolean`| Boolean indicating whether the current shared payloads are being resolved.
refreshSharePayloads| `() => void`| Forces a refresh of the shared payloads.
resolvedSharedPayloads| `ResolvedSharePayload[]`| Contains an array of resolved payloads shared with the app. Returns an empty array if the shared payloads are being resolved or if the resolving has failed.
sharedPayloads| `SharePayload[]`| Returns unresolved payloads shared with the app. Synchronous and available immediately after creating the hook.

## Config plugin types

### `ActivationRule`

Android

iOS

Web

Literal Type: `union`

Acceptable values are: `ActivationRuleOptions` | `string`

### `ActivationRuleOptions`

iOS

Describes a configuration for data types that are possible to share in the application on iOS.

Property| Type| Description
---|---|---
supportsAttachmentsWithMaxCount(optional)| `number`| Determines a maximum number of attachments that can be shared with the app. When `0` the app will not accept attachment shares.Default:`0`
supportsFileWithMaxCount(optional)| `number`| Determines a maximum number of files that can be shared with the app. When `0` the app will not accept file shares.Default:`0`
supportsImageWithMaxCount(optional)| `number`| Determines a maximum number of images that can be shared with the app. When `0` the app will not accept shared images.Default:`0`
supportsMovieWithMaxCount(optional)| `number`| Determines a maximum number of videos that can be shared with the app. When `0` the app will not accept video shares.Default:`0`
supportsText(optional)| `boolean`| Whether the app should accept shared text.Default:`false`
supportsWebPageWithMaxCount(optional)| `number`| Determines a maximum number of webpages that can be shared with the app. When `0` the app will not accept webpage shares.Default:`0`
supportsWebUrlWithMaxCount(optional)| `number`| Determines a maximum number of web URLs that can be shared with the app. When `0` the app will not accept web URL shares.Default:`0`

### `IntentFilter`

Android

iOS

Web

Property| Type| Description
---|---|---
action| `ShareAction`| -
category| `'android.intent.category.DEFAULT'`| -
data| `undefinedarray`| -
filters| `string[]`| -

### `MultiIntentFilter`

Android

iOS

Web

Type: `IntentFilter` extended by:

Property| Type| Description
---|---|---
action| `MultiShareAction`| -

### `ShareAction`

Android

iOS

Web

Literal Type: `union`

Acceptable values are: `SingleShareAction` | `MultiShareAction`

### `SingleIntentFilter`

Android

iOS

Web

Type: `IntentFilter` extended by:

Property| Type| Description
---|---|---
action| `SingleShareAction`| -