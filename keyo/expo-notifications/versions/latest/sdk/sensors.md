---
title: "ExpoSensors"
url: https://docs.expo.dev/versions/latest/sdk/sensors
---

# ExpoSensors

# Expo Sensors

A library that provides access to a device's accelerometer, barometer, motion, gyroscope, light, magnetometer, and pedometer.

Android

iOS

Web

Included in Expo Go

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-sensors)[npm](https://www.npmjs.com/package/expo-sensors)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-sensors/CHANGELOG.md)

Bundled version:

~56.0.5

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`expo-sensors` provide various APIs for accessing device sensors to measure motion, orientation, pressure, magnetic fields, ambient light, and step count.

## Installation

Terminal

Copy

`- ``npx expo install expo-sensors`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Configuration in app config

You can configure `expo-sensors` using its built-in [config plugin](/config-plugins/introduction) if you use config plugins in your project ([Continuous Native Generation (CNG)](/workflow/continuous-native-generation)). The plugin allows you to configure various properties that cannot be set at runtime and require building a new app binary to take effect. If your app does not use CNG, then you'll need to manually configure the library.

### Example app.json with config plugin

app.json

Copy


    {
      "expo": {
        "plugins": [
          [
            "expo-sensors",
            {
              "motionPermission": "Allow $(PRODUCT_NAME) to access your device motion"
            }
          ]
        ]
      }
    }


### Configurable properties

Name| Default| Description
---|---|---
`motionPermission`| `"Allow $(PRODUCT_NAME) to access your device motion"`| Only for: iOS
A string to set the `NSMotionUsageDescription` permission message or `false` to disable motion permissions.

## API


    import * as Sensors from 'expo-sensors';
    // OR
    import {
      Accelerometer,
      Barometer,
      DeviceMotion,
      Gyroscope,
      LightSensor,
      Magnetometer,
      MagnetometerUncalibrated,
      Pedometer,
    } from 'expo-sensors';


## Permissions

### Android

Starting in Android 12 (API level 31), the system has a 200Hz limit for each sensor updates.

If you need an update interval greater than 200Hz, you must add the following permissions to your app.json inside the [`expo.android.permissions`](/versions/latest/config/app#permissions) array.

Android Permission| Description
---|---
`HIGH_SAMPLING_RATE_SENSORS`| Allows an app to access sensor data with a sampling rate greater than 200 Hz.

Are you using this library in an existing React Native app?

If you're not using Continuous Native Generation ([CNG](/workflow/continuous-native-generation)) or you're using native android project manually, add `HIGH_SAMPLING_RATE_SENSORS` permission to your project's android/app/src/main/AndroidManifest.xml:


    <uses-permission android:name="android.permission.HIGH_SAMPLING_RATE_SENSORS" />


### iOS

The following usage description keys are used by this library:

Info.plist Key| Description
---|---
`NSMotionUsageDescription`| A message that tells the user why the app is requesting access to the device’s motion data.

## Available sensors

For more information, see the documentation for the sensor you are interested in:

[AccelerometerMeasures device acceleration on all platforms.](/versions/latest/sdk/accelerometer) [BarometerMeasures pressure on Android and iOS platforms.](/versions/latest/sdk/barometer) [DeviceMotionMeasures device motion on all platforms.](/versions/latest/sdk/devicemotion) [GyroscopeMeasures device rotation on all platforms.](/versions/latest/sdk/gyroscope) [MagnetometerMeasures magnetic fields on Android and iOS platforms.](/versions/latest/sdk/magnetometer) [LightSensorMeasures ambient light on Android platform.](/versions/latest/sdk/light-sensor) [PedometerMeasures steps count on Android and iOS platforms.](/versions/latest/sdk/pedometer)