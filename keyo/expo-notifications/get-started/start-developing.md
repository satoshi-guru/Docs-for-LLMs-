---
title: "Start developing"
url: https://docs.expo.dev/get-started/start-developing
---

# Start developing

# Start developing

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/get-started/start-developing.mdx)

Copy page

Make your first change to an Expo project and see it live on your device.

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/get-started/start-developing.mdx)

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

1

## Start a development server

To start the development server, run the following command:

Terminal

Copy

`- ``npx expo start`

2

## Open the app on your device

After running the command above, you will see a QR code in your terminal. Scan this QR code to open the app on your device.

If you're using an Android Emulator or iOS Simulator, you can press `A` or `I` respectively to open the app.

Having problems?

Make sure you are on the same Wi-Fi network on your computer and your device.

If it still doesn't work, it may be due to the router configuration — this is common for public networks. You can work around this by choosing the Tunnel connection type when starting the development server, then scanning the QR code again.

Terminal

Copy

`- ``npx expo start --tunnel`

> Using the Tunnel connection type will make the app reloads considerably slower than on LAN or Local, so it's best to avoid tunnel when possible. You may want to install and use an emulator or simulator to speed up development if Tunnel is required to access your machine from another device on your network.

3

## Make your first change

Open the src/app/index.tsx file in your code editor and make a change.

src/app/index.tsx

17| 17|  <ThemedView style={styles.heroSection}>
---|---|---
18| 18|  <AnimatedIcon />
19| 19|  <ThemedText type="title" style={styles.title}>
20| |  Welcome to&nbsp;Expo
| 20|  Hello World!
21| 21|  </ThemedText>
22| 22|  </ThemedView>
23| 23|

Changes not showing up on your device?

Expo Go is configured by default to automatically reload the app whenever a file is changed, but let's make sure to go over the steps to enable it in case somehow things aren't working.

  * Make sure you have the [development mode enabled in Expo CLI](/workflow/development-mode#development-mode).

  * Close the Expo app and reopen it.

  * Once the app is open again, shake your device to reveal the developer menu. Press `Cmd ⌘` \+ `D`.

  * If you see Fast Refresh enabled, toggle it. If you see Disable Fast Refresh, dismiss the developer menu. Now try making another change.


* * *

## File structure

Below, you can get familiar with the default project's file structure:

Files

src/app

src/components

src/constants

src/hooks

assets

scripts

app.json

package.json

tsconfig.json

### app

Contains the app's navigation, which is file-based. The file structure of the src/app directory determines the app's navigation.

The app has two routes defined by two files: src/app/index.tsx and src/app/explore.tsx. The layout file in src/app/_layout.tsx sets up the tab navigator using the platform-specific AppTabs component.

## Features

The default project template has the following features:

Default project

File-based routing

Android, iOS, and web support

Images

Light and dark modes

Animations

### File-based routing

The app has two screens: src/app/index.tsx and src/app/explore.tsx. The layout file in src/app/_layout.tsx sets up navigation using a platform-specific AppTabs component that uses native tabs on Android and iOS, and Expo Router UI tabs on web.

[Learn More](/router/introduction)