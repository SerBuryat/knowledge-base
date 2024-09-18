*Node.js* is an open-source, **==cross-platform JS runtime environment that enables server-side execution==** of JS code. It allows developers to **==build server-side applications using JS==**.

> *node.js runs the V8 JavaScript engine, the core of Google Chrome, outside of the browser*

TL;DR - [A quick review of Node](https://nodejs.org/en/learn/asynchronous-work/dont-block-the-event-loop#:~:text=of%20Service%20attack.-,A%20quick%20review%20of%20Node,-Node.js%20uses)
## NPM

Node.js has a built-in package manager called *npm*, which makes **==it easy to download and manage dependencies (libs and modules)==**.

## Concurrency

Node.js uses a small number of threads to handle many clients. 
In Node.js there are two types of threads: one Event Loop (aka the main loop, main thread, event thread, etc.), and a pool of `k` Workers in a Worker Pool (aka the thread-pool).



## Asynchronousity

### Callbacks

You can't know when a user is going to click a button. So, you ==**define an event handler for the click event**==. This event handler accepts a function and this function calls callback:
``` javascript title:callback.js
document
	.getElementById('button')
	// callback in {...} bellow
	.addEventListener('click', () => { });
```
A callback is a simple function that's passed as a value to another function, and will ==**only be executed when the event happens**==.

### Alternatives to callbacks

Starting with ES6, JavaScript introduced several features that help us with asynchronous code that do not involve using callbacks: **==Promises==** (*ES6*) and **==async/await==** (*ES2017*).


## Node.js I/O model

At its core, Node.js operates on a *single-threaded event loop*. Unlike traditional multi-threaded approaches used by many server-side technologies, Node.js relies on a ==**single execution thread to handle all incoming requests**==. This design decision is **==rooted in the asynchronous, non-blocking nature of JavaScript==**.

The **==*event loop*==** is what **==allows==** Node.js to **==perform non-blocking I/O operations==** - by **==offloading operations to the system kernel==** whenever possible.

In nutshell to handle *event-driven asynchronous I/O* model **==Node.js uses *libuv* library==**.

Since most modern kernels are *multi-threaded*, they can handle multiple operations executing in the background. When one of these **==operations completes==**, the **==kernel tells Node.js==** so that the appropriate **==callback may be added to the poll queue==** to eventually **==be executed==**. 

### Event loop

The *event loop* is a fundamental concept in Node.js that enables it to efficiently manage concurrent operations.

> *when Node.js starts, it initializes the event loop*

The *event loop* **==continuously listens for events, such as incoming requests or completed I/O operations, and executes the associated callback functions==**. This approach allows Node.js to perform non-blocking I/O operations, ensuring that the server **==remains responsive even under heavy loads==**.

The following diagram shows a simplified overview of the *event loop*'s order of operations:
![[node-js-event-loop-operations-order.png]]

- **timers**: this phase executes callbacks scheduled by `setTimeout()` and `setInterval()`.
- **pending callbacks**: executes I/O callbacks deferred to the next loop iteration.
- **idle, prepare**: only used internally.
- **poll**: retrieve new I/O events; execute I/O related callbacks (almost all with the exception of close callbacks, the ones scheduled by timers, and `setImmediate()`); node will block here when appropriate.
- **check**: `setImmediate()` callbacks are invoked here.
- **close callbacks**: some close callbacks, e.g. `socket.on('close', ...)`.

>*between each run of the event loop, Node.js checks if it is waiting for any asynchronous I/O or timers and shuts down cleanly if there are not any*

Each phase has a FIFO queue of callbacks to execute. While each phase is special in its own way, generally, when the event loop enters a given phase, it will perform any operations specific to that phase, then execute callbacks in that phase's queue until the queue has been exhausted or the maximum number of callbacks has executed. When the queue has been exhausted or the callback limit is reached, the event loop will move to the next phase, and so on

> [process.nextTick()](https://nodejs.org/en/learn/asynchronous-work/event-loop-timers-and-nexttick#:~:text=to%20reason%20about.-,Why%20use%20process.nextTick()%3F,-There%20are%20two) *will be processed after the current operation is completed despite of event loop current phase*

> *there is a slight discrepancy between the Windows and the Unix/Linux implementation*


### libuv

>*C library that implements the Node.js event loop and all of the asynchronous behaviors of the platform*

[link to learn from](https://docs.libuv.org/en/v1.x/design.html) 


## Node.js Event emitter

``` javascript title: node-js-event-emitter.js
const EventEmitter = require('node:events');
const eventEmitter = new EventEmitter();
```

- `on` is used to add a callback function that's going to be executed when the event is triggered
	- `eventEmitter.on('start', () => console.log('started'))`
- `emit` is used to trigger an event
	- `eventEmitter.emit('start')`


## Summary

Node.js runs JavaScript code in the Event Loop (initialization and callbacks), and offers a Worker Pool to handle expensive tasks like file I/O. Node.js scales well, sometimes better than more heavyweight approaches like Apache. The secret to the scalability of Node.js is that it uses a small number of threads to handle many clients. If Node.js can make do with fewer threads, then it can spend more of your system's time and memory working on clients rather than on paying space and time overheads for threads (memory, context-switching). But because Node.js has only a few threads, you must structure your application to use them wisely.

Here's a good rule of thumb for keeping your Node.js server speedy: _Node.js is fast when the work associated with each client at any given time is "small"_.

This applies to callbacks on the Event Loop and tasks on the Worker Pool.

Source:
- [Node.js docs](https://nodejs.org/en/learn/asynchronous-work/event-loop-timers-and-nexttick)