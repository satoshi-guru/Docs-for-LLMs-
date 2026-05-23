---
title: "Monitoring services"
url: https://docs.expo.dev/monitoring/services
---

# Monitoring services

# Monitoring services

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/monitoring/services.mdx)

Copy page

Learn how to monitor the usage of your Expo and React Native app after its release.

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/monitoring/services.mdx)

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

Once your app is released, you can track anonymized usage data to give you insights on how users use your app. This data includes which updates are in use, when users experience bugs, how the app performs in production, and more.

## EAS Insights

Expo provides the [`expo-insights`](/eas-insights/introduction) library, which tracks information related to [EAS Update](/deploy/send-over-the-air-updates). This data includes the app version, platform, OS version, and update adoption. After you install it and release production builds on the app stores, you'll be able to see additional data on your project dashboard:

Get started with the following guide:

[EAS InsightsLearn how to use EAS Insights to monitor your app.](/eas-insights/introduction)

## EAS Observe

[EAS Observe](/eas/observe/introduction) is a performance monitoring service from Expo that tracks how your app performs in production. It gives you visibility in startup metrics (such as cold launch time, time to first render, and time to interactive) from real app user sessions, rendering performance, and app user experience across different devices, networks, and conditions.

Get started with the following guide:

[EAS ObserveLearn how to use EAS Observe to monitor your app's performance.](/eas/observe/introduction)

## LogRocket

You can get more insights with [LogRocket](https://logrocket.com). LogRocket records user sessions and identifies bugs as your users use your app. You can filter sessions by update IDs and also connect to your LogRocket account on the EAS dashboard to get quick access to your app's session data.

Get started with the following guide:

[Using LogRocketLearn how to use LogRocket to monitor your app.](/guides/using-logrocket)

## Sentry

[Sentry](http://getsentry.com/) is a crash reporting platform that provides real-time insight into production deployments with information to reproduce and fix crashes.

It notifies you of exceptions or errors that your users run into while using your app and organizes them for you on a web dashboard. Reported exceptions include stacktraces, device info, version, and other relevant context automatically. You can also provide additional context that is specific to your app, such as the current route and user ID.

Get started with the following guide:

[Using SentryLearn how to use Sentry to monitor your app.](/guides/using-sentry)

## Vexo

[Vexo](https://www.vexo.co/) helps you understand how users interact with your Expo app, identify friction points, and improve engagement. It provides real-time user analytics with a simple two-line integration and offers a complete dashboard with insights into user activity, app performance, and adoption trends, along with features like heatmaps, session replays, and more.

Get started with the following guide:

[Using VexoLearn how to use Vexo to monitor your app.](/guides/using-vexo)

## BugSnag

[BugSnag](https://www.bugsnag.com/) is a stability monitoring solution that provides rich, end-to-end error reporting and analytics to reproduce and fix errors with speed and precision. BugSnag supports the full stack with open-source libraries for more than 50 platforms, including React Native.

Get started with the following guide:

[Using BugSnagLearn how to use BugSnag to monitor your app.](/guides/using-bugsnag)