>*the occurrence of events independent of the main program flow, and ways to deal with such events - [wiki](https://en.wikipedia.org/wiki/Asynchrony_(computer_programming))*

Asynchronous - **code execution in an unspecified order**.


## async vs sync

- sync and async pseudo-code examples
- sync - executing code **one line by one**
- async - executing code in **unspecified moment of time** (JS `addEventListener()` with *callback* example)


## async and multithreading

We cant compare async and mutlithreading:
- async - about execution order
- multithreading - ability of CPU to provide multiple execution threads

We can **achieve async via multithreading**:
- async could use single thread (JS and it's Event Loop example)
- async could use multithreading (Java multihreading example)






