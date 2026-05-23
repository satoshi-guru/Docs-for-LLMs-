---
title: "react-native-svg"
url: https://docs.expo.dev/versions/latest/sdk/svg
---

# react-native-svg

# react-native-svg

A library that allows using SVGs in your app.

Android

iOS

macOS

tvOS

Web

Included in Expo Go

Ask AI

[GitHub](https://github.com/react-native-community/react-native-svg)[npm](https://www.npmjs.com/package/react-native-svg)

Bundled version:

15.15.4

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`react-native-svg` allows you to use SVGs in your app, with support for interactivity and animation.

## Installation

Terminal

Copy

`- ``npx expo install react-native-svg`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project. Then, follow the [installation instructions](https://github.com/react-native-community/react-native-svg#with-react-native-cli) provided in the library's README or documentation.

## API


    import * as Svg from 'react-native-svg';


### `Svg`

A set of drawing primitives such as `Circle`, `Rect`, `Path`, `ClipPath`, and `Polygon`. It supports most SVG elements and properties. The implementation is provided by [react-native-svg](https://github.com/react-native-community/react-native-svg), and documentation is provided in that repository.

SVG

Copy

Open in Snack


    import Svg, { Circle, Rect } from 'react-native-svg';

    export default function SvgComponent(props) {
      return (
        <Svg height="50%" width="50%" viewBox="0 0 100 100" {...props}>
          <Circle cx="50" cy="50" r="45" stroke="blue" strokeWidth="2.5" fill="green" />
          <Rect x="15" y="15" width="70" height="70" stroke="red" strokeWidth="2" fill="yellow" />
        </Svg>
      );
    }


### Tips

Here are some helpful links that will get you moving fast!

  * Looking for SVGs? Try [Lucide](https://lucide.dev/).
  * Create or modify your own SVGs for free using [Figma](https://www.figma.com/).
  * Optimize your SVG with [SVGOMG](https://jakearchibald.github.io/svgomg/). This will make the code smaller and easier to work with. Be sure not to remove the `viewbox` for best results on Android.
  * Convert your SVG to an Expo component in the browser using [SVGR](https://react-svgr.com/playground/?native=true&typescript=true).


## Learn more

[Visit official documentationGet full information on API and its usage.](https://github.com/software-mansion/react-native-svg)