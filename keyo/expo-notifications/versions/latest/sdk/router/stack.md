---
title: "ExpoRouter Stack"
url: https://docs.expo.dev/versions/latest/sdk/router/stack
---

# ExpoRouter Stack

# Expo Router Stack

An Expo Router API that provides Stack navigator, toolbar, and screen components.

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

An Expo Router API that provides Stack navigator, toolbar, and screen components.

> See the [Expo Router](/versions/latest/sdk/router/index) reference for installation and configuration.

## Usage


    import { Stack } from 'expo-router';

    export default function Layout() {
      return <Stack />;
    }


For more information about using stack navigator, read the stack layout guide:

[Stack layoutLearn how to use the Stack layout in Expo Router.](/router/advanced/stack)

## API


    import { Stack } from 'expo-router';


## Components

### `Stack`

Android

iOS

tvOS

Web

Renders a native stack navigator.

### `Stack.Header`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<StackHeaderProps>`

The component used to configure header styling for a stack screen.

Use this component to set header appearance properties like blur effect, background color, and shadow visibility.

Example


    import { Stack } from 'expo-router';

    export default function Page() {
      return (
        <>
          <Stack.Header
            blurEffect="systemMaterial"
            style={{ backgroundColor: '#fff' }}
          />
          <ScreenContent />
        </>
      );
    }


Example

When used inside a layout with Stack.Screen:


    import { Stack } from 'expo-router';

    export default function Layout() {
      return (
        <Stack>
          <Stack.Screen name="index">
            <Stack.Header blurEffect="systemMaterial" />
          </Stack.Screen>
        </Stack>
      );
    }


> Note: If multiple instances of this component are rendered for the same screen, the last one rendered in the component tree takes precedence.

StackHeaderProps

### `asChild`

Android

iOS

tvOS

Web

Optional • Type: `boolean` • Default: `false`

When `true`, renders children as a custom header component, replacing the default header entirely. Use this to implement fully custom header layouts.

### `blurEffect`

iOS

Optional • Type: `BlurEffect`

The blur effect to apply to the header background on iOS. Common values include 'regular', 'prominent', 'systemMaterial', etc.

### `children`

Android

iOS

tvOS

Web

Optional • Type: `[ReactNode](https://reactnative.dev/docs/react-node)`

Child elements for custom header when `asChild` is true.

### `hidden`

Android

iOS

tvOS

Web

Optional • Type: `boolean` • Default: `false`

Whether to hide the header completely. When set to `true`, the header will not be rendered.

### `largeStyle`

iOS

Optional • Type: `StyleProp<{  backgroundColor: [ColorValue](https://reactnative.dev/docs/colors),  shadowColor: 'transparent' }>`

Style properties for the large title header (iOS).

  * `backgroundColor`: Background color of the large title header
  * `shadowColor`: Set to 'transparent' to hide the large title shadow/border


### `style`

Android

iOS

tvOS

Web

Optional • Type: `StyleProp<{  backgroundColor: [ColorValue](https://reactnative.dev/docs/colors),  color: [ColorValue](https://reactnative.dev/docs/colors),  shadowColor: 'transparent' }>`

Style properties for the standard-sized header.

  * `color`: Tint color for header elements (similar to tintColor in React Navigation)
  * `backgroundColor`: Background color of the header
  * `shadowColor`: Set to 'transparent' to hide the header shadow/border


### `transparent`

Android

iOS

tvOS

Web

Optional • Type: `boolean` • Default: `false`

Whether the header should be transparent. When `true`, the header is absolutely positioned and content scrolls underneath.

Auto-enabled when:

  * `style.backgroundColor` is 'transparent'
  * `blurEffect` is set (required for blur to work)


### `Stack.Screen`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<StackScreenProps>`

StackScreenProps

### `dangerouslySingular`

Android

iOS

tvOS

Web

Optional • Type: `[SingularOptions](/versions/v56.0.0/sdk/router#singularoptions)`

When enabled, the navigator will reuse an existing screen instead of pushing a new one.

Only supported when used inside a Layout component.

> Deprecated: Use `dangerouslySingular` instead.
>
> Only supported when used inside a Layout component.

### `getId`

Android

iOS

tvOS

Web

Optional • Type: `(__namedParameters: {  params: Record<string, any> }) => string | undefined`

Function to determine a unique ID for the screen.

### `initialParams`

Android

iOS

tvOS

Web

Optional • Type: `Record<string, any>`

Initial params to pass to the route.

Only supported when used inside a Layout component.

### `listeners`

Android

iOS

tvOS

Web

Optional • Literal type: `union`

Listeners for navigation events.

Only supported when used inside a Layout component.

Acceptable values are: `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<{  beforeRemove: EventListenerCallback<NativeStackNavigationEventMap & EventMapCore<StackNavigationState<ParamListBase>>, 'beforeRemove', true>,  blur: EventListenerCallback<NativeStackNavigationEventMap & EventMapCore<StackNavigationState<ParamListBase>>, 'blur', unknown>,  focus: EventListenerCallback<NativeStackNavigationEventMap & EventMapCore<StackNavigationState<ParamListBase>>, 'focus', unknown>,  gestureCancel: EventListenerCallback<NativeStackNavigationEventMap & EventMapCore<StackNavigationState<ParamListBase>>, 'gestureCancel', unknown>,  sheetDetentChange: EventListenerCallback<NativeStackNavigationEventMap & EventMapCore<StackNavigationState<ParamListBase>>, 'sheetDetentChange', unknown>,  state: EventListenerCallback<NativeStackNavigationEventMap & EventMapCore<StackNavigationState<ParamListBase>>, 'state', unknown>,  transitionEnd: EventListenerCallback<NativeStackNavigationEventMap & EventMapCore<StackNavigationState<ParamListBase>>, 'transitionEnd', unknown>,  transitionStart: EventListenerCallback<NativeStackNavigationEventMap & EventMapCore<StackNavigationState<ParamListBase>>, 'transitionStart', unknown> }>` | `(prop: {  navigation: any,  route: [RouteProp](https://reactnavigation.org/docs/glossary-of-terms/#route-object)<ParamListBase, string> }) => ScreenListeners<TState, TEventMap>`

### `name`

Android

iOS

tvOS

Web

Optional • Type: `string`

Name is required when used inside a Layout component.

### `options`

Android

iOS

tvOS

Web

Optional • Literal type: `union`

Options to configure the screen.

Accepts an object or a function returning an object. The function form `options={({ route }) => ({})}` is only supported when used inside a Layout component. When used inside a page component, pass an options object directly.

Acceptable values are: `[NativeStackNavigationOptions](https://reactnavigation.org/docs/native-stack-navigator#options)` | `(prop: {  navigation: any,  route: [RouteProp](https://reactnavigation.org/docs/glossary-of-terms/#route-object)<ParamListBase, string> }) => [NativeStackNavigationOptions](https://reactnavigation.org/docs/native-stack-navigator#options)`

### `redirect`

Android

iOS

tvOS

Web

Optional • Type: `boolean`

Redirect to the nearest sibling route. If all children are `redirect={true}`, the layout will render `null` as there are no children to render.

Only supported when used inside a Layout component.

#### Inherited Props

  * `PropsWithChildren`


### `Stack.SearchBar`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<StackSearchBarProps>`

A search bar component that integrates with the native stack header.

> Note: Using `Stack.SearchBar` will automatically make the header visible (`headerShown: true`), as the search bar is rendered as part of the native header.

To display the search bar in the bottom toolbar on iOS 26+, use `Stack.Toolbar.SearchBarSlot` inside `Stack.Toolbar`.

Example


    import { Stack } from 'expo-router';

    export default function Page() {
      return (
        <>
          <Stack.SearchBar
            placeholder="Search..."
            onChangeText={(text) => console.log(text)}
          />
         <ScreenContent />
        </>
      );
    }


StackSearchBarProps

#### Inherited Props

  * `SearchBarProps`


### `Stack.Title`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<StackTitleProps>`

Component to set the screen title.

Can be used inside `Stack.Screen` in a layout or directly inside a screen component.

Example

String title in a layout:


    import { Stack } from 'expo-router';

    export default function Layout() {
      return (
        <Stack>
          <Stack.Screen name="index">
            <Stack.Title large>Home</Stack.Title>
          </Stack.Screen>
        </Stack>
      );
    }


Example

String title inside a screen:


    import { Stack } from 'expo-router';

    export default function Page() {
      return (
        <>
          <Stack.Title>My Page</Stack.Title>
          <ScreenContent />
        </>
      );
    }


Example

Custom component as the title using `asChild`:


    import { Stack } from 'expo-router';

    export default function Layout() {
      return (
        <Stack>
          <Stack.Screen name="index">
            <Stack.Title asChild>
              <MyCustomTitle />
            </Stack.Title>
          </Stack.Screen>
        </Stack>
      );
    }


> Note: If multiple instances of this component are rendered for the same screen, the last one rendered in the component tree takes precedence.

StackTitleProps

### `asChild`

Android

iOS

tvOS

Web

Optional • Type: `boolean`

Use this to render a custom component as the header title.

Example


    <Stack.Title asChild>
      <MyCustomTitle />
    </Stack.Title>


### `children`

Android

iOS

tvOS

Web

Optional • Type: `[ReactNode](https://reactnative.dev/docs/react-node)`

The title content. Pass a string for a plain text title, or a custom component when `asChild` is enabled.

### `large`

iOS

Optional • Type: `boolean`

Enables large title mode.

### `largeStyle`

iOS

Optional • Type: `StyleProp<{  color: [ColorValue](https://reactnative.dev/docs/colors),  fontFamily: TextStyle[fontFamily],  fontSize: TextStyle[fontSize],  fontWeight: [Exclude](https://www.typescriptlang.org/docs/handbook/utility-types.html#excludeuniontype-excludedmembers)<TextStyle[fontWeight], number> }>`

Style properties for the large title header.

### `style`

Android

iOS

tvOS

Web

Optional • Type: `StyleProp<{  color: [ColorValue](https://reactnative.dev/docs/colors),  fontFamily: TextStyle[fontFamily],  fontSize: TextStyle[fontSize],  fontWeight: [Exclude](https://www.typescriptlang.org/docs/handbook/utility-types.html#excludeuniontype-excludedmembers)<TextStyle[fontWeight], number>,  textAlign: 'left' | 'center' }>`

### `Stack.Toolbar`

Experimental

•

Android

iOS

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<StackToolbarProps>`

The component used to configure the stack toolbar.

  * Use `placement="left"` to customize the left side of the header.
  * Use `placement="right"` to customize the right side of the header.
  * Use `placement="bottom"` (default) to show a bottom toolbar.


If multiple instances of this component are rendered for the same screen, the last one rendered in the component tree takes precedence.

> Note: Using `Stack.Toolbar` with `placement="left"` or `placement="right"` will automatically make the header visible (`headerShown: true`), as the toolbar is rendered as part of the native header.

> Note: `Stack.Toolbar` with `placement="bottom"` can only be used inside page components, not in layout components.

Example


    import { Stack } from 'expo-router';

    export default function Layout() {
      return (
        <Stack>
          <Stack.Screen name="index">
            <Stack.Toolbar placement="left">
              <Stack.Toolbar.Button icon="sidebar.left" onPress={() => alert('Left button pressed!')} />
            </Stack.Toolbar>
            <Stack.Toolbar placement="right">
              <Stack.Toolbar.Button icon="ellipsis.circle" onPress={() => alert('Right button pressed!')} />
            </Stack.Toolbar>
          </Stack.Screen>
        </Stack>
      );
    }


Example


    import { Stack } from 'expo-router';

    export default function Page() {
      return (
        <>
          <Stack.Toolbar placement="left">
            <Stack.Toolbar.Button icon="sidebar.left" onPress={() => alert('Left button pressed!')} />
          </Stack.Toolbar>
          <Stack.Toolbar>
            <Stack.Toolbar.Spacer />
            <Stack.Toolbar.Button icon="magnifyingglass" onPress={() => {}} />
            <Stack.Toolbar.Spacer />
          </Stack.Toolbar>
          <ScreenContent />
        </>
      );
    }


StackToolbarProps

### `asChild`

Android

iOS

Optional • Type: `boolean` • Default: `false`

When `true`, renders children as a custom component in the header area, replacing the default header layout.

Only applies to `placement="left"` and `placement="right"`.

### `backgroundColor`

Android

Optional • Type: `[ColorValue](https://reactnative.dev/docs/colors)`

Background color for the toolbar and its menus.

### `children`

Android

iOS

Optional • Type: `[ReactNode](https://reactnative.dev/docs/react-node)`

Child elements to compose the toolbar. Can include Stack.Toolbar.Button, Stack.Toolbar.Menu, Stack.Toolbar.View, Stack.Toolbar.Spacer, and Stack.Toolbar.SearchBarSlot (bottom placement, iOS only) components.

### `disableImePadding`

Android

Optional • Type: `boolean` • Default: `false`

When `true`, disables automatic keyboard (IME) padding on the bottom toolbar.

Only applies to `placement="bottom"` on Android.

### `placement`

Android

iOS

Optional • Type: `ToolbarPlacement` • Default: `'bottom'`

The placement of the toolbar.

  * `'left'`: Renders items in the left area of the header.
  * `'right'`: Renders items in the right area of the header.
  * `'bottom'`: Renders items in the bottom toolbar.


### `tintColor`

Android

Optional • Type: `[ColorValue](https://reactnative.dev/docs/colors)`

Tint color applied to toolbar items (buttons, menu icons, text). Individual items can override this with their own `tintColor` prop.

### `Stack.Screen.BackButton`

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

StackScreenBackButtonProps

### `children`

Android

iOS

tvOS

Web

Optional • Type: `string`

The title to display for the back button.

### `displayMode`

iOS

Optional • Type: `BackButtonDisplayMode`

The display mode for the back button.

### `hidden`

Android

iOS

tvOS

Web

Optional • Type: `boolean`

Whether to hide the back button.

### `src`

Android

iOS

tvOS

Web

Optional • Type: `[ImageSourcePropType](https://reactnative.dev/docs/image#imagesource)`

Custom image source for the back button.

### `style`

Android

iOS

tvOS

Web

Optional • Type: `StyleProp<{  fontFamily: string,  fontSize: number }>`

Style for the back button title.

### `withMenu`

iOS

Optional • Type: `boolean`

Whether to show a context menu when long pressing the back button.

### `Stack.Toolbar.Badge`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<FC<[StackToolbarBadgeProps](/versions/v56.0.0/sdk/router/stack#stacktoolbarbadgeprops)>>`

StackToolbarBadgeProps

### `children`

Android

iOS

tvOS

Web

Optional • Type: `string`

The text to display as the badge

### `style`

Android

iOS

tvOS

Web

Optional • Type: `StyleProp<[Pick](https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys)<[TextStyle](https://reactnative.dev/docs/text-style-props), 'fontFamily' | 'fontWeight' | 'color' | 'backgroundColor' | 'fontSize'>>`

### `Stack.Toolbar.Button`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<FC<StackToolbarButtonProps>>`

StackToolbarButtonProps

### `accessibilityHint`

iOS

Optional • Type: `string`

### `accessibilityLabel`

Android

iOS

Optional • Type: `string`

Accessibility label spoken by screen readers (TalkBack/VoiceOver).

> See: [Android — Compose accessibility for graphic elements](https://developer.android.com/develop/ui/compose/accessibility/api-defaults#graphic-elements) and [Apple — Supporting VoiceOver in your app](https://developer.apple.com/documentation/uikit/supporting-voiceover-in-your-app#Update-your-apps-accessibility) for more information.

### `children`

Android

iOS

tvOS

Web

Optional • Type: `[ReactNode](https://reactnative.dev/docs/react-node)`

There are two ways to specify the content of the button:

Example


    import { Stack } from 'expo-router';

    export default function Page() {
      return (
        <>
          <Stack.Toolbar placement="left">
            <Stack.Toolbar.Button icon="star.fill">As text passed as children</Stack.Toolbar.Button>
          </Stack.Toolbar>
          <ScreenContent />
        </>
      );
    }


Example


    import { Stack } from 'expo-router';

    export default function Page() {
      return (
        <>
          <Stack.Toolbar placement="left">
            <Stack.Toolbar.Button>
              <Stack.Toolbar.Icon sf="star.fill" />
              <Stack.Toolbar.Label>As components</Stack.Toolbar.Label>
              <Stack.Toolbar.Badge>3</Stack.Toolbar.Badge>
            </Stack.Toolbar.Button>
          </Stack.Toolbar>
          <ScreenContent />
        </>
      );
    }


> Note: When icon is used, the label will not be shown and will be used for accessibility purposes only. Badge is only supported in left/right placements, not in bottom (iOS toolbar limitation).

### `disabled`

Android

iOS

Optional • Type: `boolean`

### `hidden`

Android

iOS

Optional • Type: `boolean` • Default: `false`

Whether the button should be hidden.

### `hidesSharedBackground`

iOS 26+

Optional • Type: `boolean`

Whether to hide the shared background.

### `icon`

Android

iOS

Optional • Literal type: `union`

Icon to display in the button.

On Android, only image source is supported.

On iOS, it can be a string representing an SFSymbol, an image source or xcasset.

> Note: When used in `placement="bottom"` on iOS, only string SFSymbols are supported. Use the `image` prop to provide custom images.

Acceptable values are: `[ImageSourcePropType](https://reactnative.dev/docs/image#imagesource)` | `[SFSymbols7_0](https://github.com/nandorojo/sf-symbols-typescript)`

### `iconRenderingMode`

Android

iOS

Optional • Literal type: `string`

Controls how image-based icons are rendered.

  * `'template'`: applies tint color to the icon
  * `'original'`: preserves original icon colors (useful for multi-color icons)


Default behavior on iOS:

  * If `tintColor` is specified, defaults to `'template'`
  * If no `tintColor`, defaults to `'original'`


On Android: defaults to `'template'`.

This prop only affects image-based icons (not SF Symbols).

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uiimage/renderingmode-swift.enum) for more information.

Acceptable values are: `'template'` | `'original'`

### `image`

iOS

Optional • Type: `[SharedRef](/versions/v56.0.0/sdk/expo#sharedreftype)<'image'>`

Image to display in the button.

> Note: This prop is only supported in toolbar with `placement="bottom"`.

### `onPress`

Android

iOS

tvOS

Web

Optional • Type: `() => void`

### `selected`

iOS

Optional • Type: `boolean`

Whether the button is in a selected state

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uibarbuttonitem/isselected) for more information

### `separateBackground`

iOS

Optional • Type: `boolean` • Default: `false`

Whether to separate the background of this item from other header items.

### `style`

Android

iOS

Optional • Type: `StyleProp<[TextStyle](https://reactnative.dev/docs/text-style-props)>`

Style for the label of the header item.

### `tintColor`

Android

iOS

Optional • Type: `[ColorValue](https://reactnative.dev/docs/colors)`

The tint color to apply to the button item.

> See: \- [Apple documentation](https://developer.apple.com/documentation/uikit/uibarbuttonitem/tintcolor) for more information.
>
>   * [Android documentation](https://developer.android.com/develop/ui/compose/graphics/images/customize#tint-image) for more information.
>


### `variant`

iOS

Optional • Literal type: `string` • Default: `'plain'`

Acceptable values are: `'done'` | `'plain'` | `'prominent'`

### `Stack.Toolbar.Icon`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<FC<[StackToolbarIconProps](/versions/v56.0.0/sdk/router/stack#stacktoolbariconprops)>>`

StackToolbarIconProps

### `renderingMode`

Android

iOS

Optional • Literal type: `string`

Controls how the image icon is rendered.

  * `'template'`: applies tint color to the icon
  * `'original'`: preserves original icon colors


Default behavior on iOS:

  * With parent `tintColor`: defaults to `'template'`
  * Without parent `tintColor`: defaults to `'original'`


On Android: defaults to `'template'`. Setting `'original'` skips the tint so the icon's source colors are preserved.

Acceptable values are: `'template'` | `'original'`

### `src`

Android

iOS

tvOS

Web

Type: `[ImageSourcePropType](https://reactnative.dev/docs/image#imagesource)`

### `sf`

iOS

Type: `[SFSymbol](https://github.com/nandorojo/sf-symbols-typescript)`

Name of an SF Symbol to display.

### `xcasset`

iOS

Type: `string`

Name of an image in your Xcode asset catalog (`.xcassets`).

### `Stack.Toolbar.Label`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<FC<[StackToolbarLabelProps](/versions/v56.0.0/sdk/router/stack#stacktoolbarlabelprops)>>`

StackToolbarLabelProps

### `children`

Android

iOS

tvOS

Web

Optional • Type: `string`

The text to display as the label for the tab.

### `Stack.Toolbar.Menu`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<FC<StackToolbarMenuProps>>`

StackToolbarMenuProps

### `accessibilityHint`

iOS

Optional • Type: `string`

### `accessibilityLabel`

Android

iOS

Optional • Type: `string`

Accessibility label spoken by screen readers (TalkBack/VoiceOver).

> See: [Android — Compose accessibility for graphic elements](https://developer.android.com/develop/ui/compose/accessibility/api-defaults#graphic-elements) and [Apple — Supporting VoiceOver in your app](https://developer.apple.com/documentation/uikit/supporting-voiceover-in-your-app#Update-your-apps-accessibility) for more information.

### `children`

Android

iOS

Optional • Type: `[ReactNode](https://reactnative.dev/docs/react-node)`

Menu content - can include icons, labels, badges and menu actions.

Example


    <Stack.Toolbar.Menu>
      <Stack.Toolbar.Icon sfSymbol="ellipsis.circle" />
      <Stack.Toolbar.Label>Options</Stack.Toolbar.Label>
      <Stack.Toolbar.MenuAction onPress={() => {}}>Action 1</Stack.Toolbar.MenuAction>
    </Stack.Toolbar.Menu>


### `destructive`

iOS

Optional • Type: `boolean`

If `true`, the menu item will be displayed as destructive.

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uimenuelement/attributes/destructive) for more information.

### `disabled`

Android

iOS

Optional • Type: `boolean`

### `elementSize`

iOS 16.0+

Optional • Literal type: `string`

The preferred size of the menu elements.

> Note: This prop is only supported in `Stack.Toolbar.Bottom`.

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uimenu/preferredelementsize) for more information.

Acceptable values are: `'small'` | `'medium'` | `'auto'` | `'large'`

### `hidden`

Android

iOS

Optional • Type: `boolean` • Default: `false`

Whether the menu should be hidden.

### `hidesSharedBackground`

iOS 26+

Optional • Type: `boolean`

Whether to hide the shared background.

> See: [Official Apple documentation](https://developer.apple.com/documentation/uikit/uibarbuttonitem/hidessharedbackground) for more information.

### `icon`

Android

iOS

Optional • Literal type: `union`

Icon for the menu item.

Can be an SF Symbol name or an image source.

> Note: When used in `placement="bottom"` on iOS, only string SFSymbols are supported. Use the `image` prop to provide custom images.

> Note (Android): Only `ImageSourcePropType` icons are rendered at the menu root. SF Symbols and `xcasset` names are silently dropped — provide a `require()` or `{ uri }` source.

Acceptable values are: `[ImageSourcePropType](https://reactnative.dev/docs/image#imagesource)` | `[SFSymbols7_0](https://github.com/nandorojo/sf-symbols-typescript)`

### `iconRenderingMode`

Android

iOS

Optional • Literal type: `string`

Controls how image-based icons are rendered.

  * `'template'`: applies tint color to the icon (useful for monochrome icons)
  * `'original'`: preserves original icon colors (useful for multi-color icons)


Default behavior on iOS:

  * If `tintColor` is specified, defaults to `'template'`
  * If no `tintColor`, defaults to `'original'`


On Android: defaults to `'template'`.

This prop only affects image-based icons (not SF Symbols).

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uiimage/renderingmode-swift.enum) for more information.

Acceptable values are: `'template'` | `'original'`

### `image`

iOS

Optional • Type: `[SharedRef](/versions/v56.0.0/sdk/expo#sharedreftype)<'image'>`

Image to display for the menu item.

> Note: This prop is only supported in toolbar with `placement="bottom"`.

### `inline`

Android

iOS

Optional • Type: `boolean`

If `true`, the menu will be displayed inline. This means that the menu will not be collapsed.

> Note: Inline menus are only supported in submenus.

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uimenu/options-swift.struct/displayinline) for more information.

### `palette`

iOS

Optional • Type: `boolean`

If `true`, the menu will be displayed as a palette. This means that the menu will be displayed as one row.

> Note: Palette menus are only supported in submenus.

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uimenu/options-swift.struct/displayaspalette) for more information.

### `separateBackground`

iOS

Optional • Type: `boolean` • Default: `false`

Whether to separate the background of this item from other header items.

### `style`

Android

iOS

Optional • Type: `StyleProp<BasicTextStyle>`

Style for the label of the header item.

### `tintColor`

Android

iOS

Optional • Type: `[ColorValue](https://reactnative.dev/docs/colors)`

The tint color to apply to the button item.

> See: \- [Apple documentation](https://developer.apple.com/documentation/uikit/uibarbuttonitem/tintcolor) for more information.
>
>   * [Android documentation](https://developer.android.com/develop/ui/compose/graphics/images/customize#tint-image) for more information.
>


### `title`

Android

iOS

Optional • Type: `string`

Optional title to show on top of the menu.

### `variant`

iOS

Optional • Literal type: `string` • Default: `'plain'`

Acceptable values are: `'done'` | `'plain'` | `'prominent'`

### `Stack.Toolbar.MenuAction`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<FC<StackToolbarMenuActionProps>>`

StackToolbarMenuActionProps

### `children`

Android

iOS

tvOS

Web

Optional • Type: `[ReactNode](https://reactnative.dev/docs/react-node)`

Can be an Icon, Label or string title.

### `destructive`

Android

iOS

Optional • Type: `boolean`

If `true`, the menu item will be displayed as destructive.

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uimenuelement/attributes/destructive) for more information.

### `disabled`

Android

iOS

tvOS

Web

Optional • Type: `boolean`

If `true`, the menu item will be disabled and not selectable.

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uimenuelement/attributes/disabled) for more information.

### `discoverabilityLabel`

iOS

Optional • Type: `string`

An elaborated title that explains the purpose of the action.

### `hidden`

Android

iOS

tvOS

Web

Optional • Type: `boolean`

### `icon`

Android

iOS

tvOS

Web

Optional • Literal type: `union`

Icon for the menu action.

Can be an SF Symbol name or an image source.

> Note (Android): Only `ImageSourcePropType` icons are rendered. SF Symbols are silently dropped. Provide a `require()` or `{ uri }` source.

Acceptable values are: `[ImageSourcePropType](https://reactnative.dev/docs/image#imagesource)` | `[SFSymbols7_0](https://github.com/nandorojo/sf-symbols-typescript)`

### `iconRenderingMode`

iOS

Optional • Literal type: `string`

Controls how image-based icons are rendered on iOS.

  * `'template'`: iOS applies tint color to the icon (useful for monochrome icons)
  * `'original'`: Preserves original icon colors (useful for multi-color icons)


Default behavior:

  * If `tintColor` is specified, defaults to `'template'`
  * If no `tintColor`, defaults to `'original'`


This prop only affects image-based icons (not SF Symbols).

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uiimage/renderingmode-swift.enum) for more information.

Acceptable values are: `'template'` | `'original'`

### `image`

iOS

Optional • Type: `[SharedRef](/versions/v56.0.0/sdk/expo#sharedreftype)<'image'>`

Image to display for the menu action.

> Note: This prop is only supported in `Stack.Toolbar.Bottom`.

### `isOn`

Android

iOS

Optional • Type: `boolean`

If `true`, the menu item will be displayed as selected.

### `onPress`

Android

iOS

tvOS

Web

Optional • Type: `() => void`

### `subtitle`

iOS

Optional • Type: `string`

An optional subtitle for the menu item.

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uimenuelement/subtitle) for more information.

### `unstable_keepPresented`

Android

iOS

Optional • Type: `boolean`

If `true`, the menu will be kept presented after the action is selected.

This is marked as unstable, because when action is selected on iOS it will recreate the menu, which will close all opened submenus and reset the scroll position.

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uimenuelement/attributes/keepsmenupresented) for more information.

### `Stack.Toolbar.SearchBarSlot`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<FC<StackToolbarSearchBarSlotProps>>`

StackToolbarSearchBarSlotProps

### `hidden`

Android

iOS

tvOS

Web

Optional • Type: `boolean` • Default: `false`

Whether the search bar slot should be hidden.

### `hidesSharedBackground`

iOS 26+

Optional • Type: `boolean`

Whether to hide the shared background.

### `separateBackground`

iOS 26+

Optional • Type: `boolean`

Whether this search bar slot has a separate background from adjacent items. When this prop is `true`, the search bar will always render as `integratedButton`.

In order to render the search bar with a separate background, ensure that adjacent toolbar items have `separateBackground` set to `true` or use `Stack.Toolbar.Spacer` to create spacing.

Example


    <Stack.SearchBar onChangeText={()=>{}} />
    <Stack.Toolbar placement="bottom">
      <Stack.Toolbar.SearchBarSlot />
      <Stack.Toolbar.Spacer />
      <Stack.Toolbar.Button icon="square.and.pencil" />
    </Stack.Toolbar>


### `Stack.Toolbar.Spacer`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<FC<StackToolbarSpacerProps>>`

StackToolbarSpacerProps

### `hidden`

Android

iOS

Optional • Type: `boolean` • Default: `false`

Whether the spacer should be hidden.

### `sharesBackground`

iOS 26+

Optional • Type: `boolean`

Whether this spacer shares background with adjacent items.

Only available in bottom placement.

### `width`

Android

iOS

Optional • Type: `number`

The width of the spacing element.

In Left/Right placements, width is required. In Bottom placement, if width is not provided, the spacer will be flexible and expand to fill available space.

> Note: On Android, `width` is required in every placement.

### `Stack.Toolbar.View`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<FC<StackToolbarViewProps>>`

StackToolbarViewProps

### `asChild`

Android

iOS

Optional • Type: `boolean` • Default: `false`

When `true`, renders children as a custom component in the header area, replacing the default header layout.

Only applies to `placement="left"` and `placement="right"`.

### `backgroundColor`

Android

Optional • Type: `[ColorValue](https://reactnative.dev/docs/colors)`

Background color for the toolbar and its menus.

### `children`

Android

iOS

Optional • Type: `[ReactNode](https://reactnative.dev/docs/react-node)`

Child elements to compose the toolbar. Can include Stack.Toolbar.Button, Stack.Toolbar.Menu, Stack.Toolbar.View, Stack.Toolbar.Spacer, and Stack.Toolbar.SearchBarSlot (bottom placement, iOS only) components.

### `disableImePadding`

Android

Optional • Type: `boolean` • Default: `false`

When `true`, disables automatic keyboard (IME) padding on the bottom toolbar.

Only applies to `placement="bottom"` on Android.

### `placement`

Android

iOS

Optional • Type: `ToolbarPlacement` • Default: `'bottom'`

The placement of the toolbar.

  * `'left'`: Renders items in the left area of the header.
  * `'right'`: Renders items in the right area of the header.
  * `'bottom'`: Renders items in the bottom toolbar.


### `tintColor`

Android

Optional • Type: `[ColorValue](https://reactnative.dev/docs/colors)`

Tint color applied to toolbar items (buttons, menu icons, text). Individual items can override this with their own `tintColor` prop.

### `children`

Android

iOS

Optional • Type: `ReactElement<unknown, string | JSXElementConstructor<any>>`

Can be any React node.

### `hidden`

Android

iOS

Optional • Type: `boolean` • Default: `false`

Whether the view should be hidden.

### `hidesSharedBackground`

iOS 26+

Optional • Type: `boolean`

Whether to hide the shared background.

> See: [Official Apple documentation](https://developer.apple.com/documentation/uikit/uibarbuttonitem/hidessharedbackground) for more information.

### `separateBackground`

iOS

Optional • Type: `boolean` • Default: `false`

Whether to separate the background of this item from other items.

Only available in bottom placement.

## Interfaces

### `StackHeaderItemSharedProps`

Android

iOS

tvOS

Web

Property| Type| Description
---|---|---
accessibilityHint(optional)| `string`| -
accessibilityLabel(optional)| `string`| -
children(optional)| `[ReactNode](https://reactnative.dev/docs/react-node)`| -
disabled(optional)| `boolean`| -
hidesSharedBackground(optional)| `boolean`| -
icon(optional)| `[ImageSourcePropType](https://reactnative.dev/docs/image#imagesource) | [SFSymbols7_0](https://github.com/nandorojo/sf-symbols-typescript)`| -
iconRenderingMode(optional)| `'template' | 'original'`| Only for: AndroidiOS
Controls how image-based icons are rendered.

  * `'template'`: applies tint color to the icon
  * `'original'`: preserves original icon colors (useful for multi-color icons)

Default behavior on iOS:

  * If `tintColor` is specified, defaults to `'template'`
  * If no `tintColor`, defaults to `'original'`

On Android: defaults to `'template'`. The icon is always rendered through Compose's `Icon` and tinted unless `'original'` is set explicitly. This prop only affects image-based icons (not SF Symbols).

> See: [Apple documentation](https://developer.apple.com/documentation/uikit/uiimage/renderingmode-swift.enum) for more information.

separateBackground(optional)| `boolean`| -
style(optional)| `StyleProp<BasicTextStyle>`| -
tintColor(optional)| `[ColorValue](https://reactnative.dev/docs/colors)`| -
variant(optional)| `'done' | 'plain' | 'prominent'`| Default:`'plain'`