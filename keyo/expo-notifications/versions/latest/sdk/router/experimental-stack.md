---
title: "ExpoRouter Experimental Stack"
url: https://docs.expo.dev/versions/latest/sdk/router/experimental-stack
---

# ExpoRouter Experimental Stack

# Expo Router Experimental Stack

An opt-in sibling to Stack built on the react-native-screens experimental gamma stack. Available for testing only.

Android

iOS

Ask AI

[GitHub](https://github.com/expo/expo/tree/main/packages/expo-router/src/layouts/experimental-stack)[npm](https://www.npmjs.com/package/expo-router)[Changelog](https://github.com/expo/expo/tree/main/packages/expo-router/src/layouts/experimental-stack/CHANGELOG.md)

Bundled version:

~56.2.5

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

> `ExperimentalStack` is an [alpha](/more/release-statuses#alpha) API available in Expo SDK 56 and later. It is for testing only — the API and feature set may change before it is ready for production use.

`ExperimentalStack` is a sibling to [`Stack`](/versions/latest/sdk/router/stack) powered by the new `react-native-screens/experimental` stack. It is opt-in per navigator: replace `<Stack />` with `<ExperimentalStack />` in the specific layout you want to migrate, and keep `<Stack />` everywhere else.

We are sharing it early so you can try it in your app and tell us what is missing. The supported option surface is intentionally narrow and will grow over time.

> See the [Expo Router](/versions/latest/sdk/router/index) reference for more information about the file-based routing library for native and Web apps.

## Supported features

Supported screen options:

  * `title`
  * `headerShown`
  * `headerTransparent`
  * `headerBackVisible`


On Android, `ExperimentalStack` ships with [predictive back gesture](https://developer.android.com/guide/navigation/custom-back/predictive-back-gesture) support. You still need to enable it for your app by setting [`android.predictiveBackGestureEnabled`](/versions/latest/config/app#predictivebackgestureenabled) to `true` in your [app config](/workflow/configuration).

## Platform support

`ExperimentalStack` is native-only. On Web, it falls back to the standard `Stack`, so the same layout works across platforms without conditional code.

## Basic usage

app/_layout.tsx

Copy


    import { ExperimentalStack as Stack } from 'expo-router';

    export default function Layout() {
      return (
        <Stack screenOptions={{ headerShown: true }}>
          <Stack.Screen name="index" options={{ title: 'Home' }} />
          <Stack.Screen name="details" options={{ title: 'Details' }} />
        </Stack>
      );
    }


You can compose `ExperimentalStack.Screen` and `ExperimentalStack.Protected` the same way you would with `Stack`.

## Known limitations

Limited screen options

`ExperimentalStack` supports only `title`, `headerShown`, `headerTransparent`, and `headerBackVisible`. Passing any other option (for example, `headerLeft`, `headerRight`, `headerTitle`, `headerStyle`, `headerTintColor`, animation overrides, status bar options) logs a development warning and has no effect. Keep using `<Stack />` for screens that need those options.

No presentation modes yet

`ExperimentalStack` does not yet support `presentation: 'modal'` or `transparentModal`. Screens always push onto the stack.

No sheets yet

`ExperimentalStack` does not yet support `formSheet` or the related sheet sizing/detent options.

No custom headers yet

`ExperimentalStack` does not yet support custom header components or header tinting/styling. Only the four header options listed above take effect.

No animation or status bar customization yet

`ExperimentalStack` does not yet honor per-screen animation overrides (`animation`, `animationDuration`) or status bar options.

Cannot be mixed with Stack on Android

On Android, `ExperimentalStack` and the standard `Stack` cannot coexist in the same app — pick one navigator type for your native stacks. We hope to lift this restriction in a future release so you can migrate one navigator at a time.

Web falls back to standard Stack

On Web, `<ExperimentalStack />` renders the standard `Stack` from `expo-router`. Native-only options have no effect on Web.

> We are actively developing `ExperimentalStack` and looking for feedback. You can share your thoughts on [Discord](https://chat.expo.dev), [open an issue on GitHub](https://github.com/expo/expo/issues), or use the Feedback button at the bottom of this page.

## Installation

`ExperimentalStack` ships as part of `expo-router`. Follow the Expo Router installation guide if you do not already have it in your project:

[Install Expo RouterLearn how to install Expo Router in your project.](/router/installation)

## API


    import { ExperimentalStack } from 'expo-router';


## Components

### `ExperimentalStack`

Experimental

•

Android

iOS

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<[Omit](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys)<[Omit](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys)<ExperimentalStackNavigatorProps, 'children' | 'initialRouteName' | 'layout' | 'screenListeners' | 'screenOptions' | 'screenLayout' | 'UNSTABLE_router' | 'UNSTABLE_routeNamesChangeBehavior' | 'id'> & DefaultRouterOptions<string> & { children: ReactNode; layout?: ((props: { state: StackNavigationState<ParamListBase>; navigation: NavigationHelpers<ParamListBase, {}>; descriptors: Record<...>; children: ReactNode; }) => ReactElement<...>) | undefined; ... 4 more ...; UNSTABLE_routeNamesChangeBehavior?: "firstMatch" | ... 1 more ... | undefined; ..., 'children'> & [Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<[Pick](https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys)<[Omit](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys)<ExperimentalStackNavigatorProps, 'children' | 'initialRouteName' | 'layout' | 'screenListeners' | 'screenOptions' | 'screenLayout' | 'UNSTABLE_router' | 'UNSTABLE_routeNamesChangeBehavior' | 'id'> & DefaultRouterOptions<string> & { children: ReactNode; layout?: ((props: { state: StackNavigationState<ParamListBase>; navigation: NavigationHelpers<ParamListBase, {}>; descriptors: Record<...>; children: ReactNode; }) => ReactElement<...>) | undefined; ... 4 more ...; UNSTABLE_routeNamesChangeBehavior?: "firstMatch" | ... 1 more ... | undefined; ..., 'children'>>>`

Renders the new `react-native-screens/experimental` native stack.

Sibling to `Stack`. Native-only — on web it falls back to the standard `Stack`. Opt-in per navigator: replace `<Stack />` with `<ExperimentalStack />` in the specific layout you want to migrate.

### `ExperimentalStack.Screen`

Android

iOS

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<StackScreenProps>`

### `ExperimentalStack.Screen.BackButton`

Android

iOS

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<StackScreenBackButtonProps>`

Component to configure the back button.

Can be used inside Stack.Screen in a layout or directly inside a screen component.

Example


    import { Stack } from 'expo-router';

    export default function Layout() {
      return (
        <Stack>
          <Stack.Screen name="detail">
            <Stack.Screen.BackButton displayMode="minimal">Back</Stack.Screen.BackButton>
          </Stack.Screen>
        </Stack>
      );
    }


Example


    import { Stack } from 'expo-router';

    export default function Page() {
      return (
        <>
          <Stack.Screen.BackButton hidden />
          <ScreenContent />
        </>
      );
    }


> Note: If multiple instances of this component are rendered for the same screen, the last one rendered in the component tree takes precedence.

> Deprecated: Use `Stack.Title` instead.

### `ExperimentalStack.Screen.Title`

Android

iOS

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<StackTitleProps>`

## Props

### `navigation`

Android

iOS

Type: `ExperimentalStackNavigationProp<ParamList, RouteName, [NavigatorID](https://reactnavigation.org/docs/custom-navigators/#type-checking-navigators)>`

### `route`

Android

iOS

Type: `[RouteProp](https://reactnavigation.org/docs/glossary-of-terms/#route-object)<ParamList, RouteName>`

## Types

### `ExperimentalStackNavigationEventMap`

Experimental

•

Android

iOS

Navigator-level events emitted by `ExperimentalStack`. Mirrors the subset of `NativeStackNavigationEventMap` that the gamma `Stack.Screen` lifecycle callbacks can drive.

Property| Type| Description
---|---|---
gestureCancel| `{  data: undefined }`| -
transitionEnd| `{  data: {  closing: boolean } }`| -
transitionStart| `{  data: {  closing: boolean } }`| -

### `ExperimentalStackNavigationHelpers`

Android

iOS

Type: `NavigationHelpers<ParamListBase, ExperimentalStackNavigationEventMap>`

### `ExperimentalStackNavigationOptions`

Experimental

•

Android

iOS

Options accepted by `ExperimentalStack` screens. Mirrors the narrow option surface of the gamma `<Stack.HeaderConfig>` component from `react-native-screens/experimental`. Anything outside this shape is dropped with a `__DEV__` warning at runtime.

Property| Type| Description
---|---|---
headerBackVisible(optional)| `boolean`| -
headerShown(optional)| `boolean`| -
headerTransparent(optional)| `boolean`| -
title(optional)| `string`| -

### `ExperimentalStackNavigationProp`

Android

iOS

Literal Type: `union`

Acceptable values are: `NavigationProp<ParamList, RouteName, [NavigatorID](https://reactnavigation.org/docs/custom-navigators/#type-checking-navigators), StackNavigationState<ParamList>, ExperimentalStackNavigationOptions, ExperimentalStackNavigationEventMap>` | `StackActionHelpers<ParamList>`