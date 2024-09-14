- [x] - *CPS*(*continuation passing style*) ✅ 2024-06-26
- [x] - *async* via *callbacks* implemantation ✅ 2024-06-26
- [ ] - *callback*/*future*-*promise*/*async-await* is *CPS* impls
- [x] - java *virtual threads* is M:N thread model (**great** M user-mode threads scheduled to **few** N kernel(platform) threads - M >> N) ✅ 2024-06-06
- [x] - *direct memory access (DMA)* handling *I/O* without CPU ✅ 2024-07-24
- [x] - **Direct memory access (DMA)** is a feature of computer systems that allows certain hardware subsystems to **access** main system **memory independently of the CPU**. **Without DMA**, when the **CPU** is using programmed input/output, it is typically **fully occupied for the entire duration of the read or write** operation, and is thus unavailable to perform other work. **With DMA, the CPU first initiates the transfer, then it does other operations** while the transfer is in progress, and it **finally receives an interrupt from the DMA** controller (DMAC) when the operation is done. ✅ 2024-07-24
- [x] - async vs non-blocking ✅ 2024-07-24
- [ ] - *async* execution vs *sync* execution - *async* can be set only against *sync* and vice versa
- *sync* - going from up to bottom (in code), before reach code line 2 it need to
 complete a code line 1 
-  *async* - delegating some job in background and *main thread*(*execution thread*) goes forward instead wait this section of code (*this makes async is a non-blocking*), no need to wait line code 2 completion before run line code, 2 line can be run in background(another thread for example) 
- [ ] - *non-blocking execution* (one of async attributes) (**dont be confused with *non-blocking I/O*(!!!)**) - means execution which doesn't block current execution flow, *main thread* stay responsive and another thread/operation doesn't block it
- [ ] - *blocking I/O* - operations which wait for I/O completion (thread going to WAIT state or next line of code WAIT until complete)  - `read()`/`wait()`
- [ ] - *non-blocking I/O* - operations which doesn't wait I/O completion (`poll()`/`epoll()` etc.)
- [ ] - async/await mechanism 
- [ ] - async models
- [x] - CPU and *I/O* bound processes ✅ 2024-07-24
- [ ] - red-blue colored code (sync/async) - [source](https://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/)
- [ ] - `select()`, `poll()`, `epoll()`, `kqueue()` - async kernel level api (i/o multiplexing) - https://nima101.github.io/io_multiplexing
- [ ] - concurrency models
- [ ] - in *java NIO* is one thread (*may be event loop*) which server many *I/O* connections to check them if i/o complete (*epoll* linux under hood) - this mean you use **==single thread to serve many i/o operations==** instead *thread per i/o* (*if i/o compleate -> run callback with worker thread?*) - https://stackoverflow.com/questions/65490456/how-does-asynchrounous-nio-from-jdk-work (answer)
- [ ] - *file descriptor*(FD), *socket*, *I/O operation*
- [ ] - A *platform thread* is implemented as a thin wrapper around an OS(kernel) thread
- [ ] - virtual thhread(fiber-"user-mode threads") = continuation(corutine) + scheduler
- [ ] - for long running cpu-bound tasks better use kernel threads
- [ ] - virtual threads can be paused and resumed easily cause efficiant stack copying(no context switching as between threads) and scheduling mechanism
- [x] - million virtual threads(fibers) reffers to several kernel threads ✅ 2024-06-06
- [ ] - how is thread returns to the *thread pool* after jobs done
- [ ] - *containers* have single core (so using *concurreny* instead *parallelism*)
- [ ] - blocking code (I/O) running in *virtual thread* doesn't block kernel thread (only block kernel when *synchronized block(lock)* and call *native method*)
- [ ] - 1 *kernel thread* - ~1MB, 1 *virtual thread* - Java Object (size depends- few KB **==???==**)
- [ ] - virtual thread can suspend(*yield*) itself -`Continuation.yield()` and resumed by a caller - `var continuation = new Continuation(); continuation.run()`
- [ ] - *virtual thread* mounted and unmounted to/from *kernel thread* by *JVM*
- [ ] - *virtual threads* using `ForkJoinPool` scheduler by default
- [ ] - *virtual thread* can "jump" between several *kernel threads* (start with one and after sleeping continue with another)
- [ ] - `VirtualThread` consists of `Executor`, `Runnable`, `Continuation`
- [ ] - `Continuation`is not public and low-level primitive which actually gives possibilities to yield and continue *virtual thread* and represents execution state
- [ ] - *virtual thread* temporarily copied from the heap to the stack of the *сarrier*(kernel thread on which a virtual thread is mounted) *thread* when mount on and move back from stack to heap when unmount - it helps to stop and resume *virtual threads* and doent keep stack frames on *carrier thread*, this is the cost of blocking *virtual thread* and it's cheaper than block a kernel thread.
- [ ] - **==IMHO==**: *virtual threads*(in some source - `Task`) move *blocking I/O* logic from OS thread scheduler to JVM scheduler. This gives us the option of not blocking *kernel threads* during I/O and **==blocking virtual threads instead==**
- [ ] - the idea of *async* and *reactive* *proramming* - is return *thread* to *thread pool* if thread blocked by I/O
- [ ] - *virtual threads* are just new `Thread` implementation that differs in footprint(**==stacktrace keeping and managing???==**) and scheduling(by `ExecutorService`)
- [ ] - *virtual threads* best for frequent I/O blocking tasks (when a lot of threads >50-60%(**==imho==**) time is blocked by IO)
- [x] - *virtual thread* is just java object ✅ 2024-06-06
- [ ] - *coroutine* is component to allow suspend/resume task(code) execution (some kind of state machine)
- [ ] - *fibers*(virtual threads) sometimes called stackful *coroutines* cause keeps *call-stack* in memory (instead of *coroutines* which doesn't keep *call-stack*)
- [ ] - *fibers* is one of the non-blocking I/O method implementation
- [ ] - In some server programs *fibers* are used to soft block themselves(cooperative multitasking) to allow their single-threaded parent programs to continue working. In this design, fibers are used mostly for I/O access which does not need CPU processing
- [ ] - if today we use reactive stack for 50k RPS apps then with *virtual threads* we can move that point to 100-150k RPS(**==IMHO, just conception - "Netflix point"==**). Until some load point we can use sync approach with *virtual threads/fibers*
- [ ] - *coroutines/fibers* it's about concurrency, not about parallelism
- [ ] - *fiber* is stackful *coroutine* with external scheduler (which manage *fibers* outside *fibers*) 
- [ ] - when a *fiber* blocks, for instance waiting for a result that’s not yet available, other *fibers* on the same *thread* continue to run
- [ ] - *fiber/virtual thread* has *cooperative* context switching(instead *platform/kernel threads* of *preemptive* time-slicing)
- [ ] - two *fibers* on the same *kernel thread* will not run simultaneously on different processor cores
- [ ] - when the invoker calls a *coroutine*, control immediately transfers into that *coroutine*; when the *coroutine* yields, control immediately returns to its caller
- [ ] - via *virtual threads* Java tries to escape "red/blue colored code problem" (instead of using await/suspend words) - [that's why author said Java doesn't have red/blue code problem](https://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/#:~:text=Wanna%20know%20one%20that%20doesn%E2%80%99t%3F%20Java.%20I%20know%20right%3F%20How%20often%20do%20you%20get%20to%20say%2C%20%E2%80%9CYeah%2C%20Java%20is%20the%20one%20that%20really%20does%20this%20right.%E2%80%9D%3F%20But%20there%20you%20go.%20In%20their%20defense%2C%20they%20are%20actively%20trying%20to%20correct%20this%20oversight%20by%20moving%20to%20futures%20and%20async%20IO.)
- [ ] - *event loop* is **design pattern** that waits for and **dispatches events or messages** in a program and the **core mechanism that enables asynchronous operations** 
- [ ] *event loop* is what allows *Node.js* to perform *non-blocking I/O* operations cause *event loop* is the only one mechanism to do *async* stuff in JS
- [ ] - In Go, things are similar, except that we have "one OS thread per CPU" and Go has its own scheduler that gives goroutines time on those threads. Quite a lot of I/O is, in fact, handled through an event loop; e.g. when a goroutine wants to read from a socket that doesn't have data available, it registers with the *netpoller* and goes to sleep. The *netpoller* is just an *event loop* using *nonblocking I/O* and *epoll* or *kqueue* or whatever your OS has for event notification — but it's one that you don't have to manage yourself. The runtime can put an abstraction of *blocking I/O* on top of the *event loop* just by suspending a *goroutine* and running another one ([link]([https://www.reddit.com/r/golang/comments/xiu4zg/how_does_go_know_when_a_goroutine_hits_io_and_can/](https://www.reddit.com/r/golang/comments/xiu4zg/how_does_go_know_when_a_goroutine_hits_io_and_can/ "https://www.reddit.com/r/golang/comments/xiu4zg/how_does_go_know_when_a_goroutine_hits_io_and_can/")))asdasdasdasd
- [ ] - *Goriutines* (go *coroutines*) and `epoll()` non blocking io implementation([link]([https://medium.com/@chenymj23/diving-into-golang-how-does-it-effectively-wrap-the-functionality-of-epoll-26065f0654ba](https://medium.com/@chenymj23/diving-into-golang-how-does-it-effectively-wrap-the-functionality-of-epoll-26065f0654ba "https://medium.com/@chenymj23/diving-into-golang-how-does-it-effectively-wrap-the-functionality-of-epoll-26065f0654ba")))
- [ ] - OS *async* *I/O* implementations (3 different API: *kqueue*, *epoll*, *IOCP*)([link]([https://habr.com/ru/articles/600123/](https://habr.com/ru/articles/600123/ "https://habr.com/ru/articles/600123/"))):
- FreeBSD, macOS - **kqueue**, Linux - **epoll**, Windows - **I/O Completion Ports**
- **Kernel event queue** is a fast signal-delivery mechanism which **allows server software to process events from OS** in a very effective way (**notify a user-level application** about various system events)
- user app **can't access KQ data directly** and **operates with it via the API that OS** provides
- [ ] - *kqueue*, *epoll*, *IOCP* java implementations(check how it works)
- [ ] - **==the main question: How is OS notifies client code about *I/O* events?==** Is client code poll every n-time or notified from OS via some code(*native* methods *callback* may be)?
- [ ] -`wepoll()` Widnows impl of Linux `epoll()`
- [ ] - *file descriptor* is an integer that uniquely identifies an open file of the process (an abstraction for file or socket)
- [ ] - `select()`, `epoll()` and other commands use *file descriptors*.  *File descriptor* are non-negative integer points to handling *I/O resources*(file, socket etc.). *FD* mapped to *I/O op* and provide info about it: status, type, etc.  When client code start *I/O op* OS returns *FD* to client code for monitoring *FD* ( mapped *I/O op*) .
- [ ] - CPU interrupts and *interrupt service routines(ISR)* mechanisms running some code (**==handler/callback???==**) after some event happend(*I/O ops* completed, some device event)
- [ ] - Any *file descriptor* that represents a physical device relies on an interrupt. Everything else like a pipe is internal to the operating system, and therefore the kernel already knows about the activity related to the descriptor. Very old unixes and other old operating systems didn't do this, they have to poll the hardware device to see if anything had happened. This took a fair bit of CPU time.
- [ ] - *socket* event monitoring OS methods:
- `select()`
	- create and monitoring list of *fd*(max 1024) 
	- polling *fd* lineary
	- the oldest one and exists on any platform
	- measuring time in 1 nanosec
	- can't dynamically change *fd* set
- `poll()`
	- no *fd* limit (`select()` has 1024) 
	- younger then `select()` some platform doesn't have it 
	- more lightweight inner structure 
	- measuring time in 1 ms 
	- still lineary *fd* polling 
	- can't dynamically change *fd* set 
	- not for highly loaded systems(but for a huge short living *fd*(*sockets*))
- `epoll()`
	- modern *fd* polling impl 
	- return only changed *fd* from set 
	- can change set of *fd* dynamically 
	- heavier inner structure 
	- mapped context to every event(some specific data for exect event)
	- multithreading(no profit in single threaded system)
	- max profit only for high load systems(>1000 connections)
	- for long living *fd*(*sockets*)
	- only for Linux(but there is a Windows impl - [link](https://github.com/libevent/libevent/blob/master/wepoll.c) and impl in Java 21- `sun.nio.ch.WEPoll`)
- `libevent`/`libuv` library - API provides a mechanism to execute a callback function when a specific event occurs on a *fd* or after a timeout has been reached.
	- crossplatform - Linux/Windows
	- `select()`/`poll()`/`epoll()` under hood
	- heavy inner structure and huge system invocations number (cause it's not a single method but whole library)
 - [ ] - *socket* is one endpoint of a two way communication link between two programs running on the network(it's a way to talk to other computers using standard Unix *file descriptors*, every *I/O* action is done by writing or reading a *file descriptor*)
 - [ ] - A `java.nio.channels.Selector` provides a mechanism for monitoring one or more NIO channels and recognizing when one or more become available for data transfer. This way, a **single thread can be used for managing multiple channels**, and thus multiple network connections.
 - [ ]  - The class `java.nio.channels.Selector` is the linchpin(core) of Java’s *non-blocking I/O* implementation. It uses the event notification API to indicate which, among a set of non-blocking *sockets*, are ready for *I/O*.

 - [ ] - `Selector -> SelectableChannel[] -> Buffer`
 - [ ] - `Selector` itself is also an abstract class and its implementations depend on the OS - Linux `sun.nio.ch.EPollSelectorImpl` and Windows `sun.nio.ch.WindowsSelectorImpl`
 - [ ] `java.nio.*`:
 - `Selector` is the **cornerstone of non blocking I/O**.  
 - `Selector` manages multiple `Channels` , **either server or client channels**.  
- Every time you deal with a new `Channel` you register this `Channel` on the `Selector` with the events you are interested in.  
- `Channel` must `implements SelectableChannel` which support non-blocking mode  
- Then you **poll** `Selector` **with only one thread** to see if the channel is ready.  
- Essentially, `Selector` are a **wrapper for a native call** to `select(), poll()`, or a similar OS-specific system call - `wepoll(), kqueue()`.  
- The `poll()` method on `Selector` does not limit the number of sockets.  
- Optionally, the invoking thread can ask to be put to sleep until one or more of the `Channels` registered with the `Selector` is ready, or it can periodically poll the `Selector` to see if anything has become ready since the last check.
- In most cases, `Channel` have a one-to-one relationship with operating-system *FD*,or *file handles*. Although `Channel` are more generalized than *FD*, most `Channel` you will use on a regular basis are connected to open *FD*.
- `Channel` are gateways through which the native I/O services(file, network socket etc.) of the OS can be accessed with a minimum of overhead, and `Buffer` are the internal endpoints used by `Channel` to send and receive data.
- `Channel` implementations often use native code, so this is only natural. The `Channel` interfaces allow you to gain access to low-level I/O services in a controlled and portable way.
- `Channel` can operate in *blocking* or *nonblocking* modes. A `Channel` in nonblocking mode never puts the invoking *thread* to sleep. The requested operation either completes immediately or returns a result indicating that nothing was done.
- `FileChannel` are always blocking and cannot be placed into nonblocking mode. Modern OS have sophisticated caching and prefetch algorithms that usually give local disk I/O very low latency. The nonblocking paradigm of stream-oriented I/O doesn't make as much sense for file-oriented operations because of the fundamentally **different nature of file I/O(==what is diff???==)**. For file I/O, the true winner is asynchronous I/O([non-blocking I/O and async I/O](https://stackoverflow.com/questions/17615272/java-selector-is-asynchronous-or-non-blocking-architecture)???), which lets a process request one or more I/O operations from the OS but does not wait for them to complete. The process is notified at a later time that the requested I/O has completed (**==how notified ???==**). Asynchronous I/O is an advanced capability not available on many OS.
- [ ] - A name used for asynchronous I/O in the Windows API is _[overlapped I/O](https://en.wikipedia.org/wiki/Overlapped_I/O "Overlapped I/O")_.
- [ ] - [Direct memory access](https://en.wikipedia.org/wiki/Direct_memory_access "Direct memory access") (DMA) can greatly increase the efficiency of a polling-based system, and [hardware interrupts](https://en.wikipedia.org/wiki/Hardware_interrupt "Hardware interrupt") can eliminate the need for polling entirely.
- [ ] - `int channels = selector.select()`- this call blocks indefinitely if no `Channels` are ready. As soon as at least one of the registered `Channels` is ready, the selection key set of the `Selector` is updated, and the ready sets for each ready `Channel` will be updated. The return value will be the number of `Channels` determined to be ready.
- [ ] - Java 1.4 introduce `java.nio` package - non-blocking I/O via invoking system operations like `select(), poll()` etc.
- [ ] - Java 7 updated `java.nio` with async features like`AsynchronousSocketChannel`, `AsynchronousFileChannel`, `AsynchronousServerSocketChannel`.
- [ ] - Until Java 1.2 *green thread* used. Green threads have M:N thread model (M user-mode thread : N native threads)
- [ ] - Since 1.2 version Java is using 1:1 thread model (1 user-mode thread = 1 native thread)
- [ ] - Native threads is very expensive for short-lived jobs cause start/stop/switch them
- [ ] - `read()`/`write()` *system calls* just specify *FD* and block *thread* until any read/write data will be available for this *FD*
- [ ] - *thread-per-request* model uses `read()`/`write()` system calls and wait for *FD* be ready for read/write data
- [ ] - *thread-per-N-requests* uses `select()`/`poll()`/`epoll()` system calls and one thread can wait for any *FD* group ready for read/write data 
- [ ] - For *non-blocking* *sockets* OS provides `select()`, `poll()`, `epoll()` *system calls* to check whether there are events on the *socket*, such as data coming or new connections. `epoll()` scales better than `select()` or `poll()` cause return set of sockets ready for work. `epoll()` better to use with huge number >5000-10000 sockets(connections) and where > 50-60% is in waiting state(not ready for work read/write data) 
- [ ] - [Epoll and Event Loop: Just Enough You Need for Interview](https://guxi.me/posts/epoll-and-event-loop-just-enough-you-need-for-interview/#the-kernel-epoll)
- `epoll()`: 
	-  `epoll()` is a *system call* in Linux systems. On Windows you will use *IO Completion Ports* (`iocp()`), while on mac and freebsd you will have `kqueue()`. They all do similar things. 
	- `epoll()` *system call* (simple explanation):
		1) programs (*user code*) can register an event through the *system call*. Programs must provide what *file descriptor* (an abstract handle for resources, abbr. as *FD*) and what kind of event (read, write, etc.) they want to watch
		2) after it is done, programs will go to sleep
		3) kernel will be responsible to call the `poll()` method on the device
		4) once an interrupt (such as packet arrives) happens, the device says to kernel that it is ready
		5) the kernel will then copy the corresponding data to a ready list(a program may be waiting for multiple events)
		6) so kernel will tell the program what is/are ready
- *event loop*:
	- On the *userspace*, the *event loop* is often compared to the *multithreading model*. System *threads* are heavyweight, so the upper bound of the *threads* you can have becomes the bottleneck of a high traffic system. On the other hand, the *event loop* allows a single thread to be multiplexed by your busiest port - [link](https://guxi.me/posts/epoll-and-event-loop-just-enough-you-need-for-interview/#the-kernel-epoll:~:text=On%20the%20userspace%2C%20the%20event%20loop) . Instead of map every request to a thread (*thread-per-request* model), we map every request to a *event loop* (single thread or somtetime threads for *parallelism*) which dispatch it to some dedicated workers(threads) and make our app highly responsive cause *event loop* doesn't block
- [ ] - [Scalable I/O: Events- Vs Multithreading-based](https://thetechsolo.wordpress.com/2016/02/29/scalable-io-events-vs-multithreading-based/) - the best and short history of multithreading, event loops, C10K problem etc
- [ ] - async I/O vs non-blocking I/O
- [ ] - [Forms of I/O](https://en.wikipedia.org/wiki/Asynchronous_I/O#:~:text=%5Bedit%5D-,Forms%20of%20I/O,-and%20examples%20of) and examples of [POSIX](https://ru.wikipedia.org/wiki/POSIX)(**P**ortable **O**perating **S**ystem **I**nterface) functions

|         | Blocking            | Non-blocking                                    | Asynchronous                |
| ------- | ------------------- | ----------------------------------------------- | --------------------------- |
| **API** | `write()`, `read()` | `write()`, `read()` <br>+ `poll()` / `select()` | `aio_write()`, `aio_read()` |

- [ ] - [Asynchronous I/O](https://en.wikipedia.org/wiki/Asynchronous_I/O)
 - The vast majority of general-purpose computing hardware relies entirely upon **two methods** of **implementing asynchronous I/O**: **polling** and **interrupts**
 - That, in fact, is the problem with using polling in any form to synthesize a different form of asynchronous I/O. **Every CPU cycle that is a *poll* is wasted**, and lost to overhead rather than accomplishing a desired task. **Every CPU cycle that is *NOT a poll* represents an increase in latency of reaction to pending I/O**.
- [ ] - Windows has [Overlapped I/O](https://en.wikipedia.org/wiki/Overlapped_I/O) asynchronous I/O model
- [ ] - Linux has `aio_write()`/`aio_read()` asynchronous I/O model
- [ ] - Notification of I/O Completion:
- **[Polling the Status of the I/O Operation](https://www.ibm.com/docs/en/ssw_aix_72/kernelextension/polling_status_io_op.html)**(`select()`/`poll()`/`epoll()`)
- **[Asynchronously Notifying the Application When the I/O Operation Completes](https://www.ibm.com/docs/en/ssw_aix_72/kernelextension/async_notify_app_iocompl.html)** (`aio_read()`/`aio_write()`)
- **[Blocking the Application until the I/O Operation Is Complete](https://www.ibm.com/docs/en/ssw_aix_72/kernelextension/block_app_io_compl.html)** (`read()`/`write()`)
- [ ] - what the differnce between above and `io_uring()` ([[io_uring.pdf]])
- [ ] - [Trying to understand asynchronous I/O](https://www.reddit.com/r/AskComputerScience/comments/15r0auj/trying_to_understand_asynchronous_io/)
- [ ] - The whole point of an async abstraction is supposed to be to get away from a poll-driven model and instead let the runtime `notify()` us when things are ready, such as through callbacks or `await`
- [ ] - [Asynchronous File I/O via POSIX AIO or Windows Overlapped IO in Java](https://stackoverflow.com/questions/54213736/asynchronous-file-i-o-via-posix-aio-or-windows-overlapped-io-in-java):
- Java NIO means *non-blocking I/O* it is not the same as *Asynchronous I/O*. Non blocking is implemented in JAVA and long story short that means the **OS has no ability to notify runtime that operation is completed**. Instead **java uses `select()` or `poll()` system calls to check if data is available**.
- ![[io-operations-types.png]]
- That is why in JAVA the separate thread is required to constantly call *check*,*check*,*check* ...
- [ ] - **multiplexed**. This is when while you wait for one task, you start another, and while you wait for both, you start one more, and so on, until you've got many tasks all started and then, you wait idle, but on all of them. So as soon as any is done, you can proceed with handling its response, and then go back to waiting for the others. It's known as multiplexed, because while you wait, you need to check each task one after the other to see if they are done, ad vitam, until one is. It's a bit of an extension on top of normal non-blocking
- [ ] - *POSIX* -> *inter-proccess communication* -> *signal* -> C handle signals -> ... -> JVM handle signal - a stacktrace of OS events notification(**==???==**)
> ***Signals** are a way of sending simple messages which are used to **notify a process or thread of a particular event**.*
> *A signal is a standardized notification message used in Unix and POSIX-compliant operating systems. It is asynchronously sent to a running program to notify it of some event. The system interrupts the process’ normal execution to trigger a specific reaction like, among other things, terminating it. 
> So signals are a sort of **inter-process communication**.*
> *Signals are passed from the kernel to user space using a mechanism called **signal delivery**.*
> [source](How are signals handled in Linux? How are they passed to user space?)

- [ ] - [Non-blocking IO under the Hood](https://www.youtube.com/watch?v=2CjQbMGYNIk) (video)
- I/O source - HDD/SSD, network, devices: mouse, printer, micro
- blocking I/O - when process WAITING for I/O completion (thread going to SLEEP state)
- user code connect to I/O via OS kernel API (user code doesn't interact with devices directly) 
- OS provide a *socket* to a user code for I/O processes
- *socket* represent an "endpoint access of inter process communication"
- `socket()` returns *file descriptor (FD)*
- *FD* is an "address" or pointer to file/socket/pipes/device (I/O resource)
- userspace communicate with I/O resource via *FD*'s - `read(file_descriptor)` -> *All you need is FD!*
-  [blocking I/O client/server](https://github.com/ThomasPokorny/http1.1-server) example in C
- *multithreading* concurrency model give us to start several processes in parallel (threads == cores) or concurrently (threads > cores)
- *thread-per-request* model -> 1 request = 1 thread
- *thread-per-request* model has drawbacks: 1 thread = 1 MB -> bad resource's utilization, context switching, struggle for resources (deadlock, race condition)
- Peripheral devices can communicate directly to memory via *direct memory access (DMA)* instead CPU control/handle this processes
> *RAM is type of memory but DMA is peripheral used to transfer data without CPU*
- *socket* receive a request packets -> *network interface controller (NIC)* use *advanced programmable interrupt controller (APIC)* to interrupt CPU -> CPU switch in interrupt context and "link *socket* and memory" via *DMA* to communicate directly (without CPU)
- Non-blocking I/O model comes with *asynchronous* - instead *synchronous* code execution "one-by-one line" it starts execute via "when data arrive, then do something" with *continuation passing style (CPS)* via `callback`/`async/await`/`Coroutine`/`Promise`/`Feature`
- *Event loop* is core part of non-blocking I/O for monitoring multiple *FD*s (multiplexing I/O)
- *Event Loop* **polls** multiple *FD*'s for events ("Is I/O operation ready for proceed?")
- *Event Loop*  **stops** (prevent useless CPU clocks work) if no events available and wake it up when any will come (in `java.nio.Selector.select()` **blocks calling thread** until FD's event are ready)
- *Event Loop* parts: 
	- *Kernel Queue* is structure (basically linked list) in memory to keep tracks of I/O tasks (simply, when need to write/read operation to handle it's adding to *KQ*)
	- *Interest List* is structure (array) of monitored *FD*s
- [event-loop-tcp-server](https://github.com/ThomasPokorny/kqueue-tcp-server) C example
- Notions:
	- don't block *Event Loop*
	- don't use it for CPU intensive work (use worker threads and parallelization - Node.js use [libuv](https://docs.libuv.org/en/v1.x/))
	- **make sure using non-blocking I/O OS calls** (`poll`/`kqueue`/`iocp`) for *Event Loop* cause blocking I/O OS calls just `read`/`write`blocks *Event Loop* and solutions based on it (Netty/Reactor/Node.js etc.)
	- *FD*s is living in the process memory and if process dies, all corresponding *FD*s also die 
- Non-blocking I/O appends additional complexity like as "asynchronous code (*colored code*)"
- [ ] - In a ==single-threaded== application, handling ==concurrency== effectively is ==achieved== through a ==synchronous event demultiplexer== or ==event notification interface==
- [ ] - reactor pattern revolves around the concept of a handler, represented in Node.js as a ==callback function, being linked to each I/O operation==
- [ ] - Node.js reactor pattern to handle I/O operations ![[node-js-reactor-pattern.webp]]
1) Application(User) initiate I/O operation and submitting it to Event Demultiplexer with handler (callback function or application code)
2) Event Demultiplexer - platform based polling I/O mechanism (`poll()`/`select()`) for monitoring ready I/O operations
3) Event Loop - endless thread to iterate Event Queue and run ready I/O via their handler
4) Event Queue - structure for ready I/O events with their handler
5) Handler can call another I/O operation and cycle repeat
6) When Event Queue is empty, Event Loop going to block mode to wait new ready events from Event Queue
	Reactor pattern in Nodejs provides handlers that are executed at a later point in time when the corresponding I/O operations are finished (instead of waiting to each I/O is finished)
- [ ] - 