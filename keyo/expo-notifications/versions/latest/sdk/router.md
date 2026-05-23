---
title: "ExpoRouter"
url: https://docs.expo.dev/versions/latest/sdk/router
---

# ExpoRouter

# Expo Router

A file-based routing library for React Native and web applications.

Android

iOS

tvOS

Web

Included in Expo Go

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-router)[npm](https://www.npmjs.com/package/expo-router)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-router/CHANGELOG.md)

Bundled version:

~56.2.5

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`expo-router` is a routing library for React Native and web apps. It enables navigation management using a file-based routing system and provides native navigation components.

[Expo Router guidesLearn about Expo Router basics, navigation patterns, core concepts, and more.](/router/introduction)

> In SDK 56 and later, Expo Router no longer supports importing from external `@react-navigation/*` packages in application code. Repoint those imports to the matching `expo-router` entry points. Run the [codemod](/router/migrate/sdk-55-to-56#automated-migration) or follow the [SDK 55 to 56 migration guide](/router/migrate/sdk-55-to-56) to update your project.

## Installation

To use Expo Router in your project, you need to install. Follow the instructions from the Expo Router's installation guide:

[Install Expo RouterLearn how to install Expo Router in your project.](/router/installation)

## Configuration in app config

If you are using the [default](/more/create-expo#--template) template to create a new project, `expo-router`'s [config plugin](/config-plugins/introduction) is already configured in your app config.

### Example app.json with config plugin

app.json

Copy


    {
      "expo": {
        "plugins": ["expo-router"]
      }
    }


### Configurable properties

Name| Default| Description
---|---|---
`root`| `"app"`| Changes the routes directory from `app` to another value. Avoid using this property unless you have a specific need.
`origin`| `undefined`| Production origin URL where assets in the public folder are hosted. The fetch function is polyfilled to support relative requests from this origin in production. The development origin is inferred using the Expo CLI development server.
`headOrigin`| `undefined`| A more specific origin URL used in the `expo-router/head` module for iOS handoff. Defaults to `origin`.
`asyncRoutes`| `undefined`| Enable async routes (lazy loading). Can be a boolean, a string (`"development"` or `"production"`), or an object with platform-specific values (`{ android, ios, web, default }`). `production` is currently web-only and will be disabled on native.
`platformRoutes`| `true`| Enable or disable platform-specific routes (for example, index.android.tsx and index.ios.tsx).
`sitemap`| `true`| Enable or disable the automatically generated sitemap at /_sitemap.
`partialRouteTypes`| `true`| Enable partial typed routes generation. This allows TypeScript to provide type checking for routes without requiring all routes to be statically known.
`redirects`| `undefined`| An array of static redirect rules. Each rule should have `source`, `destination`, and optionally `permanent` (defaults to `false`) and `methods` (HTTP methods to redirect).
`rewrites`| `undefined`| An array of static rewrite rules. Each rule should have `source`, `destination`, and optionally `methods` (HTTP methods to rewrite).
`headers`| `undefined`| A list of headers that are set on every route response from the server. The value can be a string or an array of strings.
`disableSynchronousScreensUpdates`| `false`| Disable synchronous layout updates for native screens. This can help with performance in some cases.
`unstable_useServerMiddleware`| `false`| ExperimentalEnable server middleware support with a `+middleware.ts` file. Requires `web.output: "server"` to be set in app config.
`unstable_useServerDataLoaders`| `false`| ExperimentalEnable data loader support. This is only supported for `web.output: "static"` outputs at the moment.
`unstable_useServerRendering`| `false`| ExperimentalEnable server-side rendering. When enabled with `web.output: "server"`, HTML is rendered at request time instead of being pre-rendered at build time.

## Usage

For information core concepts, notation patterns, navigation layouts, and common navigation patterns, start with Router 101 section:

[Router 101](/router/basics/core-concepts)

## APIs

API| Description
---|---
[Stack](/versions/latest/sdk/router/stack)| Stack navigator, toolbar, and screen components
[Link](/versions/latest/sdk/router/link)| Link and Redirect components
[Color](/versions/latest/sdk/router/color)| Platform color utilities
[Native Tabs](/versions/latest/sdk/router/native-tabs)| Native tab navigation
[Split View](/versions/latest/sdk/router/split-view)| Split view layout
[UI](/versions/latest/sdk/router/ui)| Headless tab components

## API


    import { useRouter, Tabs, Navigator, Slot } from 'expo-router';


## Components

### `Badge`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<BadgeProps>`

BadgeProps

#### Inherited Props

  * `[NativeTabsTriggerBadgeProps](/versions/v56.0.0/sdk/router/native-tabs#nativetabstriggerbadgeprops)`
  * `[StackToolbarBadgeProps](/versions/v56.0.0/sdk/router/stack#stacktoolbarbadgeprops)`


### `ErrorBoundary`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<ErrorBoundaryProps>`

Props passed to a page's `ErrorBoundary` export.

ErrorBoundaryProps

### `error`

Android

iOS

tvOS

Web

Type: `[Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)`

The error that was thrown.

### `retry`

Android

iOS

tvOS

Web

Type: `() => [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

A function that will re-render the route component by clearing the `error` state.

### `ExperimentalStack`

Experimental

•

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<[Omit](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys)<[Omit](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys)<ExperimentalStackNavigatorProps, 'children' | 'initialRouteName' | 'layout' | 'screenListeners' | 'screenOptions' | 'screenLayout' | 'UNSTABLE_router' | 'UNSTABLE_routeNamesChangeBehavior' | 'id'> & DefaultRouterOptions<string> & { children: ReactNode; layout?: ((props: { state: StackNavigationState<ParamListBase>; navigation: NavigationHelpers<ParamListBase, {}>; descriptors: Record<...>; children: ReactNode; }) => ReactElement<...>) | undefined; ... 4 more ...; UNSTABLE_routeNamesChangeBehavior?: "firstMatch" | ... 1 more ... | undefined; ..., 'children'> & [Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<[Pick](https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys)<[Omit](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys)<ExperimentalStackNavigatorProps, 'children' | 'initialRouteName' | 'layout' | 'screenListeners' | 'screenOptions' | 'screenLayout' | 'UNSTABLE_router' | 'UNSTABLE_routeNamesChangeBehavior' | 'id'> & DefaultRouterOptions<string> & { children: ReactNode; layout?: ((props: { state: StackNavigationState<ParamListBase>; navigation: NavigationHelpers<ParamListBase, {}>; descriptors: Record<...>; children: ReactNode; }) => ReactElement<...>) | undefined; ... 4 more ...; UNSTABLE_routeNamesChangeBehavior?: "firstMatch" | ... 1 more ... | undefined; ..., 'children'>>>`

Renders the new `react-native-screens/experimental` native stack.

Sibling to `Stack`. Native-only — on web it falls back to the standard `Stack`. Opt-in per navigator: replace `<Stack />` with `<ExperimentalStack />` in the specific layout you want to migrate.

### `Icon`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<IconProps>`

IconProps

#### Inherited Props

  * `[NativeTabsTriggerIconProps](/versions/v56.0.0/sdk/router/native-tabs#nativetabstriggericonprops)`
  * `[StackToolbarIconProps](/versions/v56.0.0/sdk/router/stack#stacktoolbariconprops)`


### `Label`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<LabelProps>`

LabelProps

#### Inherited Props

  * `[NativeTabsTriggerLabelProps](/versions/v56.0.0/sdk/router/native-tabs#nativetabstriggerlabelprops)`
  * `[StackToolbarLabelProps](/versions/v56.0.0/sdk/router/stack#stacktoolbarlabelprops)`


### `ScrollViewStyleReset`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<React.FC>`

Root style-reset for full-screen React Native web apps with a root `<ScrollView />` should use the following styles to ensure native parity. [Learn more](https://necolas.github.io/react-native-web/docs/setup/#root-element).

### `Sitemap`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<React.FC>`

### `Slot`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<[Omit](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys)<NavigatorProps<any>, 'children'>>`

Renders the currently selected content.

There are actually two different implementations of `<Slot/>`:

  * Used inside a `_layout` as the `Navigator`
  * Used inside a `Navigator` as the content


Since a custom `Navigator` will set the `NavigatorContext.contextKey` to the current `_layout`, you can use this to determine if you are inside a custom navigator or not.

### `SuspenseFallback`

Android

iOS

tvOS

Web

Type: `React.Element<SuspenseFallbackProps>`

Props passed to a route's `SuspenseFallback` export.

SuspenseFallbackProps

### `params`

Android

iOS

tvOS

Web

Type: `Record<string, string | string[]>`

The route's URL parameters.

Example

`{ id: "123" } // For a route `./profile/[id].tsx` navigated to as `/profile/123` `

### `route`

Android

iOS

tvOS

Web

Type: `string`

The route module's `contextKey`.

Example

`./index.tsx` `./profile/[id].tsx`

### `Tabs`

Android

iOS

tvOS

Web

Renders a tabs navigator.

### `ThemeProvider`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<Props>`

### `VectorIcon`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<[VectorIconProps](/versions/v56.0.0/sdk/router#vectoriconprops)<NameT>>`

Helper component for loading vector icons.

Prefer using the `md` and `sf` props on `Icon` rather than using this component directly. Only use this component when you need to load a specific icon from a vector icon family.

Example


    import { Icon, VectorIcon } from 'expo-router';
    import MaterialCommunityIcons from '@expo/vector-icons/MaterialCommunityIcons';

    <Icon src={<VectorIcon family={MaterialCommunityIcons} name="home" />} />


VectorIconProps

### `family`

Android

iOS

tvOS

Web

Type: `{  getImageSource: (name: NameT, size: number, color: [ColorValue](https://reactnative.dev/docs/colors)) => [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<[ImageSourcePropType](https://reactnative.dev/docs/image#imagesource) | null> }`

The family of the vector icon.

Example


    import MaterialCommunityIcons from '@expo/vector-icons/MaterialCommunityIcons';


### `name`

Android

iOS

tvOS

Web

Type: `NameT`

The name of the vector icon.

### `ExperimentalStack.Screen`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<StackScreenProps>`

### `Tabs.Screen`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<ScreenProps<[TabsProps](/versions/v56.0.0/sdk/router/ui#tabsprops), [TabNavigationState](https://reactnavigation.org/docs/custom-navigators/#type-checking-navigators)<ParamListBase>, BottomTabNavigationEventMap>>`

### `ExperimentalStack.Screen.BackButton`

Android

iOS

tvOS

Web

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

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<StackTitleProps>`

## Constants

### `DarkTheme`

Android

iOS

tvOS

Web

Type: `Theme`

### `DefaultTheme`

Android

iOS

tvOS

Web

Type: `Theme`

### `unstable_navigationEvents`

Android

iOS

tvOS

Web

Type: `{  addListener: (eventType: EventType, callback: (event: Payload<EventType>) => void) => () => void,  emit: (type: EventType, event: Payload<EventType>) => void,  enable: () => void,  isEnabled: () => boolean }`

## Hooks

### `useCurrentRouteInfo()`

Experimental

•

Android

iOS

tvOS

Web

Returns route info for a screen it is called from.

Returns:

`UrlObject | undefined`

### `useFocusEffect(effect, do_not_pass_a_second_prop)`

Android

iOS

tvOS

Web

Parameter| Type| Description
---|---|---
effect| `EffectCallback`| Memoized callback containing the effect, should optionally return a cleanup function.
do_not_pass_a_second_prop(optional)| `undefined`| -




Hook to run an effect whenever a route is focused. Similar to [`React.useEffect`](https://react.dev/reference/react/useEffect), but the effect re-runs each time the screen comes into focus, and the optional cleanup function runs when the screen loses focus — not on unmount. This makes it the right primitive for refetching data, restarting subscriptions, or resetting transient screen state every time a user returns to the route.

The passed callback should be wrapped in [`React.useCallback`](https://react.dev/reference/react/useCallback) to avoid running the effect too often.

Returns:

`void`

Example


    import { useFocusEffect } from 'expo-router';
    import { useCallback } from 'react';

    export default function Route() {
      useFocusEffect(
        // Callback should be wrapped in `React.useCallback` to avoid running the effect too often.
        useCallback(() => {
          // Invoked whenever the route is focused.
          console.log("Hello, I'm focused!");

          // Return function is invoked whenever the route gets out of focus.
          return () => {
            console.log('This route is now unfocused.');
          };
        }, []),
       );

     return </>;
    }


### `useGlobalSearchParams()`

Android

iOS

tvOS

Web

Returns URL parameters for globally selected route, including dynamic path segments. This function updates even when the route is not focused. Useful for analytics or other background operations that don't draw to the screen.

Route URL example: `acme://profile/baconbrix?extra=info`.

When querying search params in a stack, opt-towards using `useLocalSearchParams` because it will only update when the route is focused.

> Note: For usage information, see [Local versus global search parameters](/router/reference/url-parameters#local-versus-global-url-parameters).

Returns:

`RouteOutputParams<TRoute> & TParams`

Example

app/profile/[user].tsx

Copy


    import { Text } from 'react-native';
    import { useGlobalSearchParams } from 'expo-router';

    export default function Route() {
      // user=baconbrix & extra=info
      const { user, extra } = useGlobalSearchParams();

      return <Text>User: {user}</Text>;
    }


### `useIsFocused()`

Android

iOS

tvOS

Web

Hook to get the current focus state of the screen. Returns a `true` if screen is focused, otherwise `false`. This can be used if a component needs to render something based on the focus state.

Returns:

`boolean`

### `useLoaderData()`

Android

iOS

tvOS

Web

Returns the result of the `loader` function for the calling route.

Returns:

`LoaderFunctionResult<T>`

Example

app/profile/[user].tsx

Copy


    import { Text } from 'react-native';
    import { useLoaderData } from 'expo-router';

    export function loader() {
      return Promise.resolve({ foo: 'bar' }};
    }

    export default function Route() {
     const data = useLoaderData<typeof loader>(); // { foo: 'bar' }

     return <Text>Data: {JSON.stringify(data)}</Text>;
    }


### `useLocalSearchParams()`

Android

iOS

tvOS

Web

Returns the URL parameters for the contextually focused route. Useful for stacks where you may push a new screen that changes the query parameters. For dynamic routes, both the route parameters and the search parameters are returned.

Route URL example: `acme://profile/baconbrix?extra=info`.

To observe updates even when the invoking route is not focused, use `useGlobalSearchParams`.

> Note: For usage information, see [Local versus global search parameters](/router/reference/url-parameters#local-versus-global-url-parameters).

Returns:

`RouteOutputParams<TRoute> & TParams`

Example

app/profile/[user].tsx

Copy


    import { Text } from 'react-native';
    import { useLocalSearchParams } from 'expo-router';

    export default function Route() {
     // user=baconbrix & extra=info
     const { user, extra } = useLocalSearchParams();

     return <Text>User: {user}</Text>;
    }


### `useNavigation(parent)`

Android

iOS

tvOS

Web

Parameter| Type| Description
---|---|---
parent(optional)| `string | [HrefObject](/versions/v56.0.0/sdk/router#hrefobject)`| Provide an absolute path such as `/(root)` to the parent route or a relative path like `../../` to the parent route.




Returns the navigation object for the current route. Mirrors the React Navigation [`navigation` object](https://reactnavigation.org/docs/navigation-object). Use it to imperatively access layout-specific functionality like `navigation.openDrawer()` in a [Drawer](/router/advanced/drawer) layout.

Returns:

`T`

The navigation object for the current route.

> See: The full navigation API is available directly from `expo-router` — no `@react-navigation/*` install required. For the navigator-dependent functions reference, see [navigation dependent functions](https://reactnavigation.org/docs/navigation-object/#navigator-dependent-functions).

Example

app/index.tsx

Copy


    import { useNavigation } from 'expo-router';

    export default function Route() {
      // Access the current navigation object for the current route.
      const navigation = useNavigation();

      return (
        <View>
          <Text onPress={() => {
            // Open the drawer view.
            navigation.openDrawer();
          }}>
            Open Drawer
          </Text>
        </View>
      );
    }


When using nested layouts, you can access higher-order layouts by passing a secondary argument denoting the layout route. For example, `/menu/_layout.tsx` is nested inside `/app/orders/`, you can use `useNavigation('/orders/menu/')`.

Example

app/orders/menu/index.tsx

Copy


    import { useNavigation } from 'expo-router';

    export default function MenuRoute() {
      const rootLayout = useNavigation('/');
      const ordersLayout = useNavigation('/orders');

      // Same as the default results of `useNavigation()` when invoked in this route.
      const parentLayout = useNavigation('/orders/menu');
    }


If you attempt to access a layout that doesn't exist, an error such as `Could not find parent navigation with route "/non-existent"` is thrown.

### `useNavigationContainerRef()`

Android

iOS

tvOS

Web

Returns:

`NavigationContainerRefWithCurrent<[RootParamList](https://reactnavigation.org/docs/typescript/#navigator-specific-types)>`

The root `<NavigationContainer />` ref for the app. The `ref.current` may be `null` if the `<NavigationContainer />` hasn't mounted yet.

### `usePathname()`

Android

iOS

tvOS

Web

Returns the currently selected route location without search parameters. For example, `/acme?foo=bar` returns `/acme`. Segments will be normalized. For example, `/[id]?id=normal` becomes `/normal`.

Returns:

`string`

Example

app/profile/[user].tsx

Copy


    import { Text } from 'react-native';
    import { usePathname } from 'expo-router';

    export default function Route() {
      // pathname = "/profile/baconbrix"
      const pathname = usePathname();

      return <Text>Pathname: {pathname}</Text>;
    }


> Deprecated: Use `useNavigationContainerRef` instead, which returns a React `ref`.

### `useRootNavigation()`

Android

iOS

tvOS

Web

Returns:

`[NavigationContainerRef](https://reactnavigation.org/docs/navigating-without-navigation-prop)<[RootParamList](https://reactnavigation.org/docs/typescript/#navigator-specific-types)> | null`

### `useRootNavigationState()`

Android

iOS

tvOS

Web

Returns the navigation state of the root navigator — the top-level navigator that contains the current screen.

Returns:

`[NavigationState](https://reactnavigation.org/docs/navigation-state)`

The current `NavigationState` of the root navigator.

> See: React Navigation's [navigation state](https://reactnavigation.org/docs/navigation-state/) reference for the shape of the returned object.

Example


    import { useRootNavigationState } from 'expo-router';

    export default function Route() {
     const { routes } = useRootNavigationState();

     return <Text>{routes[0].name}</Text>;
    }


### `useRoute()`

Android

iOS

tvOS

Web

Hook to access the route prop of the parent screen anywhere.

Returns:

`T`

Route prop of the parent screen.

### `useRoutePath()`

Android

iOS

tvOS

Web

Hook to get the path for the current route based on linking options.

Returns:

`string | undefined`

Path for the current route.

### `useRouter()`

Android

iOS

tvOS

Web

Returns the Router object for imperative navigation.

Returns:

`ImperativeRouter`

Example


    import { useRouter } from 'expo-router';
    import { Text } from 'react-native';

    export default function Route() {
     const router = useRouter();

     return (
      <Text onPress={() => router.push('/home')}>Go Home</Text>
     );
    }


### `useScrollToTop(ref)`

Android

iOS

tvOS

Web

Parameter| Type
---|---
ref| `[RefObject](https://react.dev/reference/react/useRef)<ScrollableWrapper>`




Returns:

`void`

### `useSegments()`

Android

iOS

tvOS

Web

Returns a list of selected file segments for the currently selected route. Segments are not normalized, so they will be the same as the file path. For example, `/[id]?id=normal` becomes `["[id]"]`.

Returns:

`RouteSegments<TSegments>`

Example

app/profile/[user].tsx

Copy


    import { Text } from 'react-native';
    import { useSegments } from 'expo-router';

    export default function Route() {
      // segments = ["profile", "[user]"]
      const segments = useSegments();

      return <Text>Hello</Text>;
    }


`useSegments` can be typed using an abstract. Consider the following file structure:


    - app
      - [user]
        - index.tsx
        - followers.tsx
      - settings.tsx


This can be strictly typed using the following abstract with `useSegments` hook:


    const [first, second] = useSegments<['settings'] | ['[user]'] | ['[user]', 'followers']>()


### `useServerDocumentContext()`

Android

iOS

tvOS

Web

Returns the server document data for server-side rendering, including `<html>`/`<body>` attributes and additional nodes to add to `<head>`/`<body>` for metadata and assets.

Returns:

`ServerDocumentData`

Example


    import { useServerDocumentContext } from 'expo-router/html';

    export default function Root({ children }) {
      const { htmlAttributes, bodyAttributes, headNodes, bodyNodes } = useServerDocumentContext();
      return (
        <html {...htmlAttributes}>
          <head>{headNodes}</head>
          <body {...bodyAttributes}>
            {children}
            {bodyNodes}
          </body>
        </html>
      );
    }


### `useSitemap()`

Android

iOS

tvOS

Web

Returns:

`SitemapType | null`

### `useTheme()`

Android

iOS

tvOS

Web

Returns:

`Theme`

## Methods

### `withLayoutContext(Nav, processor, useOnlyUserDefinedScreens)`

Android

iOS

tvOS

Web

Parameter| Type| Description
---|---|---
Nav| `T`| The navigator component to wrap.
processor(optional)| `(options: ScreenProps[]) => ScreenProps[]`| A function that processes the screens before passing them to the navigator.
useOnlyUserDefinedScreens(optional)| `boolean`| If true, all screens not specified as navigator's children will be ignored.Default:`false`




Returns a navigator that automatically injects matched routes and renders nothing when there are no children. Return type with `children` prop optional.

Enables use of other built-in React Navigation navigators and other navigators built with the React Navigation custom navigator API.

Returns:

`[Component](https://react.dev/reference/react/Component)<PropsWithoutRef<PickPartial<ComponentProps<T>, 'children'>>> & {  Protected: FunctionComponent<ProtectedProps>,  Screen: (props: ScreenProps<TOptions, TState, TEventMap>) => null }`

Example

app/_layout.tsx

Copy


    import { ParamListBase, TabNavigationState } from "@react-navigation/native";
    import {
      createMaterialTopTabNavigator,
      MaterialTopTabNavigationOptions,
      MaterialTopTabNavigationEventMap,
    } from "@react-navigation/material-top-tabs";
    import { withLayoutContext } from "expo-router";

    const MaterialTopTabs = createMaterialTopTabNavigator();

    const ExpoRouterMaterialTopTabs = withLayoutContext<
      MaterialTopTabNavigationOptions,
      typeof MaterialTopTabs.Navigator,
      TabNavigationState<ParamListBase>,
      MaterialTopTabNavigationEventMap
    >(MaterialTopTabs.Navigator);

    export default function TabLayout() {
      return <ExpoRouterMaterialTopTabs />;
    }


## Interfaces

### `ActionDispatchedEvent`

Android

iOS

tvOS

Web

Property| Type| Description
---|---|---
actionType| `string`| The action type from the dispatched NavigationAction (e.g. `NAVIGATE`).
payload| `object | undefined`| -
state| `ReactNavigationState`| -
type| `'actionDispatched'`| -

### `PageBlurredEvent`

Android

iOS

tvOS

Web

Extends: `BasePageEvent`

Property| Type| Description
---|---|---
type| `'pageBlurred'`| -

### `PageFocusedEvent`

Android

iOS

tvOS

Web

Extends: `BasePageEvent`

Property| Type| Description
---|---|---
type| `'pageFocused'`| -

### `PagePreloadedEvent`

Android

iOS

tvOS

Web

Extends: `BasePageEvent`

The page rendered as part of a preload (e.g. `router.prefetch()`) and is not currently focused. If the user later navigates to this route, the matching `pageFocused` will fire then; the preload may also be invalidated or the route unmounted (`pageRemoved`) without a focus.

Property| Type| Description
---|---|---
type| `'pagePreloaded'`| -

### `PageRemoved`

Android

iOS

tvOS

Web

Extends: `BasePageEvent`

Property| Type| Description
---|---|---
type| `'pageRemoved'`| -

## Types

### `AnalyticsEvent`

Android

iOS

tvOS

Web

Literal Type: `union`

Acceptable values are: `PagePreloadedEvent` | `PageFocusedEvent` | `PageBlurredEvent` | `PageRemoved` | `ActionDispatchedEvent`

### `EffectCallback()`

Android

iOS

tvOS

Web

Memoized callback containing the effect, should optionally return a cleanup function.

Returns:

`undefined | void | () => void`

### `ExperimentalStackNavigationEventMap`

Experimental

•

Android

iOS

tvOS

Web

Navigator-level events emitted by `ExperimentalStack`. Mirrors the subset of `NativeStackNavigationEventMap` that the gamma `Stack.Screen` lifecycle callbacks can drive.

Property| Type| Description
---|---|---
gestureCancel| `{  data: undefined }`| -
transitionEnd| `{  data: {  closing: boolean } }`| -
transitionStart| `{  data: {  closing: boolean } }`| -

### `ExperimentalStackNavigationOptions`

Experimental

•

Android

iOS

tvOS

Web

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

tvOS

Web

Literal Type: `union`

Acceptable values are: `NavigationProp<ParamList, RouteName, [NavigatorID](https://reactnavigation.org/docs/custom-navigators/#type-checking-navigators), StackNavigationState<ParamList>, ExperimentalStackNavigationOptions, ExperimentalStackNavigationEventMap>` | `StackActionHelpers<ParamList>`

### `ExternalPathString`

Android

iOS

tvOS

Web

Literal Type: `union`

Acceptable values are: `{string}:{string}` | `//{string}`

### `Href<T>`

Android

iOS

tvOS

Web

The main routing type for Expo Router. It includes all available routes with strongly typed parameters. It can either be:

  * string: A full path like `/profile/settings` or a relative path like `../settings`.
  * object: An object with a `pathname` and optional `params`. The `pathname` can be a full path like `/profile/settings` or a relative path like `../settings`. The params can be an object of key-value pairs.


An Href can either be a string or an object.

Generic: `T`

Type: `T ? T[href] : string | [HrefObject](/versions/v56.0.0/sdk/router#hrefobject)`

### `HrefObject`

Android

iOS

tvOS

Web

Property| Type| Description
---|---|---
params(optional)| `UnknownInputParams`| Optional parameters for the route.
pathname| `string`| The path of the route.

### `ImperativeRouter`

Android

iOS

tvOS

Web

Returns `router` object for imperative navigation API.

Example


    import { router } from 'expo-router';
    import { Text } from 'react-native';

    export default function Route() {

     return (
      <Text onPress={() => router.push('/home')}>Go Home</Text>
     );
    }


Property| Type| Description
---|---|---
back| `() => void`| Goes back in the navigation history.
canDismiss| `() => boolean`| Checks if it is possible to dismiss the current screen. Returns `true` if the router is within the stack with more than one screen in stack's history.
canGoBack| `() => boolean`| Navigates to a route in the navigator's history if it supports invoking the `back` function.
dismiss| `(count: number) => void`| Navigates to the a stack lower than the current screen using the provided count if possible, otherwise 1. If the current screen is the only route, it will dismiss the entire stack.
dismissAll| `() => void`| Returns to the first screen of the closest stack — equivalent to a stack `popToTop` action.

> See: React Navigation's [`popToTop`](https://reactnavigation.org/docs/stack-actions/#poptotop) stack action for the underlying behavior.

dismissTo| `(href: [Href](/versions/v56.0.0/sdk/router#hreft), options: [NavigationOptions](https://reactnavigation.org/docs/screen-options/)) => void`| Dismisses screens until the provided href is reached. If the href is not found, it will instead replace the current screen with the provided `href`.
navigate| `(href: [Href](/versions/v56.0.0/sdk/router#hreft), options: [NavigationOptions](https://reactnavigation.org/docs/screen-options/)) => void`| Navigates to the provided `href`.
prefetch| `(name: [Href](/versions/v56.0.0/sdk/router#hreft)) => void`| Prefetch a screen in the background before navigating to it
push| `(href: [Href](/versions/v56.0.0/sdk/router#hreft), options: [NavigationOptions](https://reactnavigation.org/docs/screen-options/)) => void`| Navigates to the provided `href` using a push operation if possible.
replace| `(href: [Href](/versions/v56.0.0/sdk/router#hreft), options: [NavigationOptions](https://reactnavigation.org/docs/screen-options/)) => void`| Navigates to route without appending to the history. Can be used with `useFocusEffect` to redirect imperatively to a new screen.

> See: [Using `useRouter()` hook](/router/reference/redirects) to redirect.

setParams| `(params: [Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<RouteInputParams<T>>) => void`| Updates the current route's query params.

### `NativeIntent`

Android

iOS

tvOS

Web

Created by using a special file called `+native-intent.tsx` at the top-level of your project's app directory. It exports `redirectSystemPath` or `legacy_subscribe` functions, both methods designed to handle URL/path processing.

Useful for re-writing URLs to correctly target a route when unique/referred URLs are incoming from third-party providers or stale URLs from previous versions.

> See: For more information on how to use `NativeIntent`, see [Customizing links](/router/advanced/native-intent).

Property| Type| Description
---|---|---
legacy_subscribe(optional)| `(listener: (url: string) => void) => undefined | void | () => void`|

> Experimentally available in SDK 52.

Useful as an alternative API when a third-party provider doesn't support Expo Router but has support for React Navigation via `Linking.subscribe()` for existing projects. Using this API is not recommended for newer projects or integrations since it is incompatible with Server Side Routing and [Static Rendering](/router/reference/static-rendering), and can become challenging to manage while offline or in a low network environment.
redirectSystemPath(optional)| `(event: {  initial: boolean,  path: string }) => [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null> | string | null`| A special method used to process URLs in native apps. When invoked, it receives an `options` object with the following properties:

  * path: represents the URL or path undergoing processing.
  * initial: a boolean indicating whether the path is the app's initial URL.

Its return value should be a `string`, a `Promise<string | null>`, or `null`. When a falsy value is returned (for example, `null`), no redirection occurs and the app stays on the current path. Note that throwing errors within this method may result in app crashes. It's recommended to wrap your code inside a `try/catch` block and utilize `.catch()` when appropriate.

> See: For usage information, see [Redirecting system paths](/router/advanced/native-intent#redirectsystempath).

### `PickPartial`

Android

iOS

tvOS

Web

Literal Type: `union`

The list of input keys will become optional, everything else will remain the same.

Acceptable values are: `[Omit](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys)<T, K>` | `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<[Pick](https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys)<T, K>>`

### `RedirectConfig`

Android

iOS

tvOS

Web

Property| Type| Description
---|---|---
destination| `string`| -
destinationContextKey| `string`| -
external(optional)| `boolean`| -
methods(optional)| `string[]`| -
permanent(optional)| `boolean`| -
source| `string`| -

### `RelativePathString`

Android

iOS

tvOS

Web

Literal Type: `union`

Acceptable values are: `./{string}` | `../{string}` | `'..'`

### `ResultState`

Android

iOS

tvOS

Web

Type: `PartialState<[NavigationState](https://reactnavigation.org/docs/navigation-state)>` extended by:

Property| Type| Description
---|---|---
state(optional)| `ResultState`| -

### `RoutePath`

Android

iOS

tvOS

Web

Type: `[Exclude](https://www.typescriptlang.org/docs/handbook/utility-types.html#excludeuniontype-excludedmembers)<Extract[pathname], RelativePathString | ExternalPathString>`

### `ScreenProps`

Android

iOS

tvOS

Web

Property| Type| Description
---|---|---
dangerouslySingular(optional)| `[SingularOptions](/versions/v56.0.0/sdk/router#singularoptions)`| -
getId(optional)| `({ params }: {  params: Record<string, any> }) => string | undefined`| -
initialParams(optional)| `Record<string, any>`| -
listeners(optional)| `ScreenListeners<TState, TEventMap> | (prop: {  navigation: any,  route: [RouteProp](https://reactnavigation.org/docs/glossary-of-terms/#route-object)<ParamListBase, string> }) => ScreenListeners<TState, TEventMap>`| -
name(optional)| `string`| Name is required when used inside a Layout component.
options(optional)| `TOptions | (prop: {  navigation: any,  route: [RouteProp](https://reactnavigation.org/docs/glossary-of-terms/#route-object)<ParamListBase, string> }) => TOptions`| -
redirect(optional)| `boolean`| Redirect to the nearest sibling route. If all children are `redirect={true}`, the layout will render `null` as there are no children to render.

### `SearchOrHash`

Android

iOS

tvOS

Web

Literal Type: `union`

Acceptable values are: `?{string}` | `#{string}`

### `SingularOptions`

Android

iOS

tvOS

Web

Type: `boolean` or `object` shaped as below:

#### `(name, params) => `string | undefined``

Parameter| Type| Description
---|---|---
name[(index signature)](https://www.typescriptlang.org/docs/handbook/2/objects.html#index-signatures)| `string`| -
params[(index signature)](https://www.typescriptlang.org/docs/handbook/2/objects.html#index-signatures)| `UnknownOutputParams`| -

### `SitemapType`

Android

iOS

tvOS

Web

Property| Type| Description
---|---|---
children| `SitemapType[]`| -
contextKey| `string`| -
filename| `string`| -
href| `string | [Href](/versions/v56.0.0/sdk/router#hreft)`| -
isGenerated| `boolean`| -
isInitial| `boolean`| -
isInternal| `boolean`| -