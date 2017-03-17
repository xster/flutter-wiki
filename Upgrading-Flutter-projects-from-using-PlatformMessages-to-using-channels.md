# Introduction
Prior to pull requests [flutter/flutter#8837](https://github.com/flutter/flutter/pull/8837) and [flutter/engine#3482](https://github.com/flutter/engine/pull/3482), both merged on March 17 2017, communication between Flutter app components written in Dart and host platform app components written for Android or iOS was accomplished by sending and receiving string/JSON messages using methods defined in the following types:

* `PlatformMessages` (Dart)
* `FlutterView` (Android)
* `FlutterViewController` (iOS)

These methods have been removed, leaving only methods for sending unencoded binary messages. A new channel concept has been introduced in their place:

* `PlatformMessageChannel`, `PlatformMethodChannel` (Dart)
* `FlutterMessageChannel`, `FlutterMethodChannel` (Android, iOS)

The main goals of the new API are to achieve

* higher-level communication: asynchronous method calls and event streams
* less boilerplate and redundancy
* a more symmetric API across Dart, Android, and iOS

The following sections detail how to port code written against the old API.

# Flutter side

## Sending messages to platform

Replace code like this

    some old code

with code like this

    some new code

## Receiving messages from platform

Replace code like this

    some old code

with code like this

    some new code
