>async means that things can happen independently of the main program flow

Asynchronous - **code execution in an unspecified order**.

## async programming

**Conception** of programming based on **async function result**.
> async function: 
> - doesn't block the calling [[thread]] - **non-blocking**
> - has wrapper which returns immediately (Java  `Future` or JS `Promise`)
> - the real computation occurs in background with dedicated thread (multithreading) or with another approaches ([[event loop]], [[coroutine]]s(virtual threads), [[reactive programming]] etc.)
> - after a while function set a value into wrapper `Future` or `Promise`
## async vs sync

- sync and async pseudo-code examples
- sync - executing code **one line by one**
- async - executing code in **unspecified moment of time** (JS `addEventListener()` with *callback* example)


## async and multithreading

We cant compare async and multithreading:
- async - about execution order
- multithreading - ability of CPU to provide multiple execution threads

We can **achieve async**:
- async could use single thread (JS and it's Event Loop example)
- async could use multithreading (Java multithreading example)

## async and concurrency

> don't confuse apples and oranges..

- multithreading leads to concurrency (*common CPU or resources usage*)
- async can avoid concurrency (*don't use CPU or resources simultaneously cause can use single thread or other models*)







