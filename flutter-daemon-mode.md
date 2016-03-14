# Flutter Daemon

## Overview

The `flutter` command-line tool supports a daemon server mode for use by IDEs and other tools.

```
flutter daemon
```

It runs a persistent, JSON-RPC based server to communicate with devices. IDEs and and other tools can start the flutter tool in this mode and get device addition and removal notifications, as well as being able to programmatically start and stop apps on those devices.

## Protocol

The daemon speaks [JSON-RPC](http://json-rpc.org/) to clients. It uses stdin and stdout as the protocol transport. To send a command to the server, create your command as a JSON-RPC message, encode it to json, surround the encoded text with square brackets, and write it as one line of text to stdout:

```
TODO:
```

TODO:

## Domains and Commands

### daemon domain

#### version

The `version()` command responds with a String with the protocol version.

#### shutdown

The `shutdown()` command will terminate the flutter daemon. It is not necessary to call this before shutting down the daemon; it is perfectly acceptable to just kill the daemon process.

TODO: daemon.logMessage

### app domain

#### start

TODO: start

#### stop

The `stop()` command takes two parameters, a `deviceId` and a `projectDirectory`. It returns a `bool` to indicate success or failure in stopping an app.

### device domain

TODO: getDevices
TODO: enable
TODO: disable

TODO: device.added
TODO: device.removed

## Source

See the [source](https://github.com/flutter/flutter/blob/master/packages/flutter_tools/lib/src/commands/daemon.dart) for the daemon protocol and implementation.