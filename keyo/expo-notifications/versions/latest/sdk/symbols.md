---
title: "ExpoSymbols"
url: https://docs.expo.dev/versions/latest/sdk/symbols
---

# ExpoSymbols

# Expo Symbols

A library that allows access to native symbols.

Android

iOS

tvOS

Web

Included in Expo Go

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-symbols)[npm](https://www.npmjs.com/package/expo-symbols)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-symbols/CHANGELOG.md)

Bundled version:

~56.0.5

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

> This library is currently in [beta](/more/release-statuses#beta) and subject to breaking changes.

`expo-symbols` provides access to native symbol libraries across platforms. On iOS and tvOS, it uses [SF Symbols](https://developer.apple.com/sf-symbols/). On Android and web, it uses [Material Symbols](https://fonts.google.com/icons).

## Installation

Terminal

Copy

`- ``npx expo install expo-symbols`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Usage

### Cross-platform symbols

Pass an object with per-platform symbol names to render symbols on all platforms. Browse available iOS symbols in the [Apple SF Symbols app](https://developer.apple.com/sf-symbols/) and Android/web symbols at [Google Material Symbols](https://fonts.google.com/icons).

App.js

Copy


    import { SymbolView } from 'expo-symbols';
    import { StyleSheet, View } from 'react-native';

    export default function App() {
      return (
        <View style={styles.container}>
          <SymbolView
            name={{ ios: 'info.circle', android: 'info', web: 'info' }}
            tintColor="#007AFF"
            size={35}
          />
          <SymbolView
            name={{
              ios: 'pencil.tip.crop.circle.badge.plus',
              android: 'home_and_garden',
              web: 'home_and_garden',
            }}
            style={styles.symbol}
          />
        </View>
      );
    }

    const styles = StyleSheet.create({
      container: {
        flex: 1,
        backgroundColor: '#fff',
        alignItems: 'center',
        justifyContent: 'center',
      },
      symbol: {
        width: 35,
        height: 35,
        margin: 5,
      },
    });


If you only pass a string, it is treated as an SF Symbol name and renders only on iOS. On Android and web, nothing will be rendered unless you provide a `fallback`:


    {
      /* iOS-only: pass an SF Symbol name directly */
    }
    <SymbolView name="airpods.chargingcase" style={styles.symbol} type="hierarchical" />;

    {
      /* Use fallback for platforms where the symbol is not defined */
    }
    <SymbolView name={{}} fallback={<Text>?</Text>} />;


### Weights

On iOS, pass a weight string directly. On Android, import a weight object from `expo-symbols/androidWeights`:


    import bold from 'expo-symbols/androidWeights/bold';

    <SymbolView
      name={{ ios: 'star.fill', android: 'star', web: 'star' }}
      weight={{ ios: 'bold', android: bold }}
      tintColor="gold"
      size={35}
    />;


Available weight imports: `bold`, `semiBold`, `medium`, `regular`, `light`, `extraLight`, `thin`.

## API


    import { SymbolView } from 'expo-symbols';


## Component

### `SymbolView`

Android

iOS

tvOS

Web

Type: `React.[Element](https://www.typescriptlang.org/docs/handbook/jsx.html#function-component)<SymbolViewProps>`

SymbolViewProps

### `animationSpec`

iOS

Optional • Type: `AnimationSpec`

The animation configuration to apply to the symbol.

### `colors`

iOS

Optional • Literal type: `union`

An array of colors to use when the `SymbolType` is `palette`.

Acceptable values are: `[ColorValue](https://reactnative.dev/docs/colors)` | `[ColorValue[]](https://reactnative.dev/docs/colors)`

### `fallback`

Android

iOS

tvOS

Web

Optional • Type: `React.ReactNode`

Fallback to render when a symbol for the given platform is not defined.

### `name`

Android

iOS

tvOS

Web

Literal type: `union`

The name of the symbol. iOS Symbols can be viewed in the [Apple SF Symbols app](https://developer.apple.com/sf-symbols/).

Acceptable values are: `[SFSymbol](https://github.com/nandorojo/sf-symbols-typescript)` | `{  android: AndroidSymbol,  ios: [SFSymbol](https://github.com/nandorojo/sf-symbols-typescript),  web: AndroidSymbol }`

### `resizeMode`

iOS

Optional • Type: `ContentMode` • Default: `'scaleAspectFit'`

Determines how the image should be resized to fit its container.

### `scale`

iOS

Optional • Type: `SymbolScale` • Default: `'unspecified'`

The scale of the symbol to render.

### `size`

Android

iOS

tvOS

Web

Optional • Type: `number` • Default: `24`

The size of the symbol.

### `tintColor`

Android

iOS

tvOS

Web

Optional • Type: `[ColorValue](https://reactnative.dev/docs/colors)`

The tint color to apply to the symbol.

### `type`

iOS

Optional • Type: `SymbolType` • Default: `'monochrome'`

Determines the symbol variant to use.

### `weight`

Android

iOS

tvOS

Web

Optional • Literal type: `union` • Default: `'unspecified'`

The weight of the symbol to render. On Android and web import from `expo-symbols/androidWeights/{weight}`.

Acceptable values are: `SymbolWeight` | `{  android: AndroidSymbolWeight,  ios: SymbolWeight }`

#### Inherited Props

  * `[ViewProps](https://reactnative.dev/docs/view#props)`


## Methods

### `Symbol.unstable_getMaterialSymbolSourceAsync(symbol, size, color)`

Android

Parameter| Type
---|---
symbol| `See description for available values.`
size| `number`
color| `string`




Renders a Material Symbol to an image source. Useful for APIs that require an `ImageSourcePropType` instead of a component, such as tab bar icons.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<[ImageSourcePropType](https://reactnative.dev/docs/image#imagesource) | null>`

## Types

### `AnimationEffect`

Android

iOS

tvOS

Web

Property| Type| Description
---|---|---
direction(optional)| `'up' | 'down'`| The direction of the animation.
type| `AnimationType`| The type of animation to apply to the symbol.
wholeSymbol(optional)| `boolean`| Whether the entire symbol should animate or just the individual layers.Default:`false`

### `AnimationSpec`

Android

iOS

tvOS

Web

The animation configuration to apply to the symbol.

Property| Type| Description
---|---|---
effect(optional)| `AnimationEffect`| The effect to apply to the symbol.
repeatCount(optional)| `number`| The number of times the animation should repeat.
repeating(optional)| `boolean`| If the animation should repeat.
speed(optional)| `number`| The duration of the animation in seconds.
variableAnimationSpec(optional)| `VariableAnimationSpec`| An object that specifies how the symbol’s layers should animate.

### `AnimationType`

Android

iOS

tvOS

Web

Literal Type: `string`

The type of animation to apply to the symbol.

Acceptable values are: `'bounce'` | `'pulse'` | `'scale'`

### `ContentMode`

Android

iOS

tvOS

Web

Literal Type: `string`

Determines how the image should be resized to fit its container.

Acceptable values are: `'scaleToFill'` | `'scaleAspectFit'` | `'scaleAspectFill'` | `'redraw'` | `'center'` | `'top'` | `'bottom'` | `'left'` | `'right'` | `'topLeft'` | `'topRight'` | `'bottomLeft'` | `'bottomRight'`

### `SymbolScale`

Android

iOS

tvOS

Web

Literal Type: `string`

The scale of the symbol to render.

Acceptable values are: `'default'` | `'unspecified'` | `'small'` | `'medium'` | `'large'`

### `SymbolType`

Android

iOS

tvOS

Web

Literal Type: `string`

Determines the symbol variant to use.

  * `'monochrome'` \- Creates a color configuration that specifies that the symbol image uses its monochrome variant.

  * `'hierarchical'` \- Creates a color configuration with a color scheme that originates from one color.

  * `'palette'` \- Creates a color configuration with a color scheme from a palette of multiple colors.

  * `'multicolor'` \- Creates a color configuration that specifies that the symbol image uses its multicolor variant, if one exists.


Acceptable values are: `'monochrome'` | `'hierarchical'` | `'palette'` | `'multicolor'`

### `SymbolWeight`

Android

iOS

tvOS

Web

Literal Type: `string`

The weight of the symbol to render.

Acceptable values are: `'unspecified'` | `'ultraLight'` | `'thin'` | `'light'` | `'regular'` | `'medium'` | `'semibold'` | `'bold'` | `'heavy'` | `'black'`

### `VariableAnimationSpec`

Android

iOS

tvOS

Web

A variable color animation draws attention to a symbol by changing the opacity of the symbol’s layers. You can choose to apply the effect to layers either cumulatively or iteratively. For cumulative animations, each layer’s opacity remains changed until the end of the animation cycle. For iterative animations, each layer’s opacity changes briefly before returning to its original state. These effects are compounding, each value set to `true` will add an additional effect.

Property| Type| Description
---|---|---
cumulative(optional)| `boolean`| This effect enables each successive variable layer, and the layer remains enabled until the end of the animation cycle. This effect cancels the iterative variant.
dimInactiveLayers(optional)| `boolean`| An effect that dims inactive layers of a symbol. This effect draws inactive layers with reduced, but nonzero, opacity.
hideInactiveLayers(optional)| `boolean`| An effect that hides inactive layers of a symbol. This effect hides inactive layers completely, rather than drawing them with reduced, but nonzero, opacity.
iterative(optional)| `boolean`| An effect that momentarily enables each layer of a symbol in sequence.
nonReversing(optional)| `boolean`| An effect that doesn’t reverse each time it repeats.
reversing(optional)| `boolean`| An effect that reverses each time it repeats.