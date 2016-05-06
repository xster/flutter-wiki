We aspire to reach a state where developers are able to use the following modes for running Flutter code. Our tools should not expose any other combinations of features or modes.

1. Debug mode: Turns on all the assertions in the world, includes all debugging information, enables all the debugger aids (e.g. observatory) and service extensions. Optimises for fast develop/run cycles. Does not optimise for execution speed, binary size, or deployment.

2. Release mode: Turns off all assertions, strips as much debugging information as possible, turns off all the debugger tools. Optimises for fast startup, fast execution, small package sizes. Disables any debugging aids.  Disables service extensions. Intended for deployment to end-users.

3. Profile mode: Same as release mode except that profile-mode service extensions (like the one that turns on the performance overlay) is enabled, and tracing is enabled, as well as the minimum required to support using the tracing information (e.g. observatory can probably connect to the process).

All the above use the same Flutter engine build (one for each supported device platform). For our purposes during development, we would also have headless desktop host debug builds (for tests), and debug engine builds for each supported device platform.