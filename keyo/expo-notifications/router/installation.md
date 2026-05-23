---
title: "Manual installation"
url: https://docs.expo.dev/router/installation
---

# Manual installation

# Manual installation

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/router/installation.mdx)

Copy page

Learn how to add Expo Router to an existing project with these detailed instructions.

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/router/installation.mdx)

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

Follow the steps below if you have an existing project and want to add Expo Router. For new projects, see the [Quick start](/router/introduction#quick-start) in the introduction guide.

Prerequisites

1 requirement

Set up your development environment

Make sure your computer is [set up for running an Expo app](/get-started/create-a-project).

1

### Install dependencies

You'll need to install the following dependencies:

Terminal

Copy

`- ``npx expo install expo-router react-native-safe-area-context react-native-screens expo-linking expo-constants expo-status-bar`

The above command will install versions of these libraries that are compatible with the Expo SDK version your project is using.

2

### Setup entry point

For the property `main`, use the `expo-router/entry` as its value in the package.json. The initial client file is [src/app/_layout.tsx](/router/reference/src-directory) (or [app/_layout.tsx](/router/basics/navigation-layouts#root-layout) if not using the src directory).

package.json

Copy


    {
      "main": "expo-router/entry"
    }


Custom entry point to initialize and load side-effects

You can create a custom entry point in your Expo Router project to initialize and load side-effects before your app loads the root layout (src/app/_layout.tsx). Below are some of the common cases for a custom entry point:

  * Initializing global services like analytics, error reporting, and so on.
  * Setting up polyfills
  * Ignoring specific logs using `LogBox` from `react-native`


  1. Create a new file in the root of your project, such as index.js. After creating this file, the project structure should look like this:

`src`

`app`

`_layout.tsx`

`index.js`

`package.json`

`Other project files`

  2. Import or add your custom configuration to the file. Then, import `expo-router/entry` to register the app entry. Remember to always import it last to ensure all configurations are properly set up before the app renders.

index.js

Copy

         // Import side effects first and services

         // Initialize services

         // Register app entry through Expo Router
         import 'expo-router/entry';


  3. Update the `main` property in package.json to point to the new entry file.

package.json

Copy

         {
           "main": "index.js"
         }



3

### Modify project configuration

Add a deep linking `scheme` and enable [typed routes](/router/reference/typed-routes) in your [app config](/workflow/configuration):

app.json

Copy


    {
      "expo": {
        "scheme": "your-app-scheme",
        "experiments": {
          "typedRoutes": true
        }
      }
    }


If you are developing your app for web, install the following dependencies:

Terminal

Copy

`- ``npx expo install react-native-web react-dom`

Then, enable [Metro web](/guides/customizing-metro#adding-web-support-to-metro) support by adding the following to your [app config](/workflow/configuration):

app.json

Copy


    {
      "expo": {
        "web": {
          "bundler": "metro"
        }
      }
    }


4

### Modify babel.config.js

If your project has a babel.config.js file, ensure you use `babel-preset-expo` as the `preset`. If you don't need any custom Babel configuration, you can delete the file entirely:

babel.config.js

Copy


    module.exports = function (api) {
      api.cache(true);
      return {
        presets: ['babel-preset-expo'],
      };
    };


5

### Configure path aliases

If you are using the [`src` directory](/router/reference/src-directory), add path aliases to your tsconfig.json so you can use short import paths like `@/components/button` instead of relative paths:

tsconfig.json

Copy


    {
      "extends": "expo/tsconfig.base",
      "compilerOptions": {
        "strict": true,
        "paths": {
          "@/*": ["./src/*"]
        }
      },
      "include": ["**/*.ts", "**/*.tsx", ".expo/types/**/*.ts", "expo-env.d.ts"]
    }


The `@/*` alias maps to the src directory in the above example.

6

### Clear bundler cache

After updating the configuration, run the following command to clear the bundler cache:

Terminal

Copy

`- ``npx expo start --clear`

7

### Update resolutions

If you're upgrading from an older version of Expo Router, ensure you remove all outdated Yarn resolutions or npm overrides in your package.json. Specifically, remove `metro`, `metro-resolver`, `react-refresh` resolutions from your package.json.