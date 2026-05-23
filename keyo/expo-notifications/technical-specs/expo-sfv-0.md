---
title: "Expo Structured Field Values"
url: https://docs.expo.dev/technical-specs/expo-sfv-0
---

# Expo Structured Field Values

# Expo Structured Field Values

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/technical-specs/expo-sfv-0.mdx)

Copy page

Version 0

[Edit page](https://github.com/expo/expo/edit/main/docs/pages/technical-specs/expo-sfv-0.mdx)

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

Structured Field Values for HTTP, [IETF RFC 8941](https://tools.ietf.org/html/rfc8941), is a proposal to formalize header syntax and facilitate nested data.

Since it is still a work in progress, Expo maintains a custom version that only implements the following subset of the protocol defined in [IETF RFC 8941](https://tools.ietf.org/html/rfc8941):

  * All key values
  * String, integer, and decimal items
  * Dictionaries