Main Thread - The main path for the code execution (async and sync operations are both started). Event Loop cannot begin until main thread has executed.
Event Loop - Starts after the main thread executes (this means async functions have been triggered as well). Once the event loop is running, it takes callbacks off of the callback queue, and runs them as they become available to run
Call Stack - a stack of function calls/operations
Web APIs - 
Callback Queue - a separate stack for callback functions. collects callbacks when task is complete (web )


Function Calls are not asynchronous, but side effect can be asynchronous

backend = when software is running on the computer
frontend = when software is running on the browser

Sync = If a property/method has "sync" in the name, it means it returns a promise. (returned promises are just data, so you. the .then() method should be passed a parameter, you can just name it data)

Event Handler = code that defines what to do in response to an event. Involves LISTENING then ACTING. Frequently uses the '.on' method.

