---
title: "ExpoDocumentPicker"
url: https://docs.expo.dev/versions/latest/sdk/document-picker
---

# ExpoDocumentPicker

# Expo DocumentPicker

A library that provides access to the system's UI for selecting documents from the available providers on the user's device.

Android

iOS

Web

Included in Expo Go

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-document-picker)[npm](https://www.npmjs.com/package/expo-document-picker)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-document-picker/CHANGELOG.md)

Bundled version:

~56.0.4

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`expo-document-picker` provides access to the system's UI for selecting documents from the available providers on the user's device.

## Installation

Terminal

Copy

`- ``npx expo install expo-document-picker`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Configuration in app config

You can configure `expo-document-picker` using its built-in [config plugin](/config-plugins/introduction) if you use config plugins in your project ([Continuous Native Generation (CNG)](/workflow/continuous-native-generation)). The plugin allows you to configure various properties that cannot be set at runtime and require building a new app binary to take effect. If your app does not use CNG, then you'll need to manually configure the library.

### Example app.json with config plugin

If you want to enable [iCloud storage features](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_icloud-services), set the `expo.ios.usesIcloudStorage` key to `true` in the [app config](/workflow/configuration) file as specified [configuration properties](/versions/latest/config/app#usesicloudstorage).

Running [EAS Build](/build/introduction) locally will use [iOS capabilities signing](/build-reference/ios-capabilities) to enable the required capabilities before building.

app.json

Copy


    {
      "expo": {
        "plugins": [
          [
            "expo-document-picker",
            {
              "iCloudContainerEnvironment": "Production"
            }
          ]
        ]
      }
    }


### Configurable properties

Name| Default| Description
---|---|---
`iCloudContainerEnvironment`| `undefined`| Only for: iOS
Sets the iOS `com.apple.developer.icloud-container-environment` entitlement used for AdHoc iOS builds. Possible values: `Development`, `Production`. [Learn more](https://github.com/expo/eas-cli/issues/693).
`kvStoreIdentifier`| `undefined`| Only for: iOS
Overrides the default iOS `com.apple.developer.ubiquity-kvstore-identifier` entitlement, which uses your Apple Team ID and bundle identifier. This may be needed if your app was transferred to another Apple Team after enabling iCloud storage.

Are you using this library in an existing React Native app?

Apps that don't use [EAS Build](/build/introduction) and want [iCloud storage features](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_icloud-services) must [manually configure](/build-reference/ios-capabilities#manual-setup) the [iCloud service with CloudKit support](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_icloud-container-environment) for their bundle identifier.

If you enable the iCloud capability through the [Apple Developer Console](/build-reference/ios-capabilities#apple-developer-console), then be sure to add the following entitlements in your `ios/[app]/[app].entitlements` file (where `dev.expo.my-app` if your bundle identifier):


    <key>com.apple.developer.icloud-container-identifiers</key>
    <array>
        <string>iCloud.dev.expo.my-app</string>
    </array>
    <key>com.apple.developer.icloud-services</key>
    <array>
        <string>CloudDocuments</string>
    </array>
    <key>com.apple.developer.ubiquity-container-identifiers</key>
    <array>
        <string>iCloud.dev.expo.my-app</string>
    </array>
    <key>com.apple.developer.ubiquity-kvstore-identifier</key>
    <string>$(TeamIdentifierPrefix)dev.expo.my-app</string>


Apple Developer Console also requires an iCloud Container to be created. When registering the new container, you are asked to provide a description and identifier for the container. You may enter any name under the description. Under the identifier, add `iCloud.<your_bundle_identifier>` (same value used for `com.apple.developer.icloud-container-identifiers` and `com.apple.developer.ubiquity-container-identifiers` entitlements).

## Using with `expo-file-system`

When using `expo-document-picker` with [`expo-file-system`](/versions/latest/sdk/filesystem), it's not always possible for the file system to read the file immediately after the `expo-document-picker` picks it.

To allow the `expo-file-system` to read the file immediately after it is picked, you'll need to ensure that the [`copyToCacheDirectory`](/versions/latest/sdk/document-picker#documentpickeroptions) option is set to `true`.

## API


    import * as DocumentPicker from 'expo-document-picker';


## Component

### `getDocumentAsync`

Android

iOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<DocumentPickerOptions>`

Display the system UI for choosing a document. By default, the chosen file is copied to [the app's internal cache directory](/versions/latest/sdk/filesystem#filesystemcachedirectory).

> Notes for Web: The system UI can only be shown after user activation (e.g. a `Button` press). Therefore, calling `getDocumentAsync` in `componentDidMount`, for example, will not work as intended. The `cancel` event will not be returned in the browser due to platform restrictions and inconsistencies across browsers.

## Types

### `DocumentPickerAsset`

Android

iOS

Web

Property| Type| Description
---|---|---
base64(optional)| `string`| Only for: Web
Base64 string of the file.
file(optional)| `File`| Only for: Web
`File` object for the parity with web File API.
lastModified| `number`| Timestamp of last document modification. [Web API specs](https://developer.mozilla.org/en-US/docs/Web/API/File/lastModified) The lastModified provides the last modified date of the file as the number of milliseconds since the Unix epoch (January 1, 1970 at midnight). Files without a known last modified date return the current date.
mimeType(optional)| `string`| Document MIME type.
name| `string`| Document original name.
size(optional)| `number`| Document size in bytes.
uri| `string`| A URI to the local document file.

### `DocumentPickerCanceledResult`

Android

iOS

Web

Type representing canceled pick result.

Property| Type| Description
---|---|---
assets| `null`| Always `null` when the request was canceled.
canceled| `true`| Always `true` when the request was canceled.
output(optional)| `null`| Only for: Web
Always `null` when the request was canceled.

### `DocumentPickerOptions`

Android

iOS

Web

Property| Type| Description
---|---|---
base64(optional)| `boolean`| Only for: Web
If `true`, asset url is base64 from the file If `false`, asset url is the file url parameterDefault:`true`
copyToCacheDirectory(optional)| `boolean`| Only for: AndroidiOS
If `true`, the picked file is copied to [`FileSystem.CacheDirectory`](/versions/latest/sdk/filesystem#filesystemcachedirectory), which allows other Expo APIs to read the file immediately. This may impact performance for large files, so you should consider setting this to `false` if you expect users to pick particularly large files and your app does not need immediate read access.Default:`true`
multiple(optional)| `boolean`| Allows multiple files to be selected from the system UI.Default:`false`
type(optional)| `string | string[]`| The [MIME type(s)](https://en.wikipedia.org/wiki/Media_type) of the documents that are available to be picked. It also supports wildcards like `'image/*'` to choose any image. To allow any type of document you can use `'*/*'`.Default:`'*/*'`

### `DocumentPickerResult`

Android

iOS

Web

Literal Type: `union`

Type representing successful and canceled document pick result.

Acceptable values are: `DocumentPickerSuccessResult` | `DocumentPickerCanceledResult`

### `DocumentPickerSuccessResult`

Android

iOS

Web

Type representing successful pick result.

Property| Type| Description
---|---|---
assets| `DocumentPickerAsset[]`| An array of picked assets.
canceled| `false`| If asset data have been returned this should always be `false`.
output(optional)| `[FileList](https://developer.mozilla.org/en-US/docs/Web/API/FileList)`| Only for: Web
`FileList` object for the parity with web File API.