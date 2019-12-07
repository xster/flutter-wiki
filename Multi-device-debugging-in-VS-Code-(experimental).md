The Flutter plugin for Visual Studio code has been updated to support [Visual Studio Code’s multi-target debugging](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging).

## Requirements
*   You must be on latest Flutter master channel for concurrent builds to not overwrite each other and cause build failures.

## Known Issues
*   The Hot Reload button on the debug toolbar does all sessions, but Hot Restart only does the active session


## Setup
To debug multiple devices concurrently you should set up a launch config for each device that has the `deviceId` field set (this is the same ID you'd pass to `flutter run -d xxx`). Open the launch config by clicking `Debug -> Open Configurations`. Add a `compound` config at the bottom that will launch both (or more) configurations at the same time:

```json
{
	"version": "0.2.0",
	"configurations": [
		{
			"name": "Current Device",
			"request": "launch",
			"type": "dart"
		},
		{
			"name": "Android",
			"request": "launch",
			"type": "dart",
			"deviceId": "android"
		},
		{
			"name": "iPhone",
			"request": "launch",
			"type": "dart",
			"deviceId": "iphone"
		},
	],
	"compounds": [
		{
			"name": "All Devices",
			"configurations": ["Android", "iPhone"],
		}
	]
}
```

Once saved, the compound configuration will show up in the drop-down at the top of the Debug side bar.

## Running
Selecting the compound config on the Debug side bar and clicking `Debug -> Start Debugging` (or `Start Without Debugging`) will launched debug sessions for each device at the same time.

Note: This may be slow because there are multiple concurrent builds (it may be faster on subsequent builds). You will also see multiple progress notification windows during the builds (we can likely improve this, but right now they just run without knowledge of each other so spawn their own notifications).

When there are multiple active debug sessions, the debug toolbar will show a dropdown that lets you select the "active" debug session.

Actions on the debug toolbar will be sent only to this session (so you can step individually). The Debug Console will show a similar dropdown to switch between the output of each session.

The Variables and Watch panels on the Debug sidebar will be applied to the active debug session (being re-evaluated as you switch between them), though the Call Stack will show all sessions together, grouped by name.

All the usual functionality like hot-reload-on-save should work. Terminating an app should close only that single debug session.
