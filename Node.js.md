*Node.js* is an open-source, **==cross-platform JS runtime environment that enables server-side execution==** of JS code. It allows developers to **==build server-side applications using JS==**, the same language that is used for client-side scripting in web browsers.

## NPM

Node.js has a built-in package manager called *npm*, which makes **==it easy to download and manage additional modules and libraries==**. This allows developers to easily add new functionality to their applications.

## Node.js I/O model
At its core, Node.js operates on a *single-threaded event loop*. Unlike traditional multi-threaded approaches used by many server-side technologies, Node.js relies on a ==**single execution thread to handle all incoming requests**==. This design decision is **==rooted in the asynchronous, non-blocking nature of JavaScript==**.

### Event loop

The *event loop* is a fundamental concept in Node.js that enables it to efficiently manage concurrent operations. 
The event loop **==continuously listens for events, such as incoming requests or completed I/O operations, and executes the associated callback functions==**. This approach allows Node.js to perform non-blocking I/O operations, ensuring that the server **==remains responsive even under heavy loads==**.

In nutshell to handle event-driven asynchronous I/O model Node.js uses *libuv* library.

### libuv

[link to learn from](https://docs.libuv.org/en/v1.x/design.html) 