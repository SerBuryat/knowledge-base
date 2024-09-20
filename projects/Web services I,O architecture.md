>*First of all, this material must provide practical side - use this knowledge in programming and web services development*: "How web service works inside and handle outside interactions(file system/network)?","Why it might be slow or occupy idle OS resources?", "What could make web service be faster and more scalable under high load?", etc.

The goal is to tell about different I/O model architectures:
- How they work and handle I/O operations
- What is blocking and non-blocking i/o
- What is sync and async
- What is reactive programming
- Pros/cons of each architecture
- Where to use and how to choose right web service model (from theory to practice)

Next, show and explain those models via 3 apps: 
- **app v.1**- sync and blocking architecture
	- sync code execution (imperative style)
	- blocking i/o operations
	- thread-per-request model
- **app v.2** - async and non-blocking architecture
	- mixed sync and async code execution
	- mixed blocking and non-blocking i/o operations
	- threads are divided by roles:
		- event loop - i/o polling thread(s)(i/o multiplexing) + responsiveness
		- worker threads - handle requests/responses
- **app v.3** - reactive architecture
	- pub/sub implementation of Observer pattern
	- async code execution via functions
	- non-blocking i/o operations
	- reactive drivers for external i/o systems(db, kafka, rest api, etc.) 
	- thread model based on actual ReactiveX language realization

Need a picture for every app:
- 3 parts (from bottom to up): hardware device, OS (OS API), user app (developed with some programming language)
	- OS responsibility is to provide API to App code for I/O processes(via `read()`/`write()`, `select()`/`poll()`/`epoll()`, etc.)
	- If OS has no this API - app code cannot work with I/O by itself
	- Programming language must provide interface to OS API for developers
- in some cases,actually, need 2 app pics: one for server and one for client (to show request/response flow)
- flow:
	- Client:
		- app - start HTTP request
		- OS API - handle request
		- hardware - send it to server
	- Server:
		- hardware - receive request
		- OS API - handle request
		- app - receive request
	- show how it started and handled on all levels
	- what's happened with calling thread(s) on client and server on every stage of request/response process (who/when/why - block/non-block)