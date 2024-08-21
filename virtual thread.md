> *started by Project Loom in 2017 and introduced in Java 19*

> *virtual thread can be correlated to [[fiber]](stackfull [[coroutine]])*

Virtual [[thread]] is **==just [[java]] object==** - new **==implementation==** of `java.lang.Thread` **==controlled by JVM runtime==** (instead of *OS* threads *scheduler*).
![[virtual-thread-extends-thread.png]]

- **==Virtual threads are not faster threads==**; they do not run code any faster than platform threads.
- **==They exist to provide scale (higher throughput), not speed (lower latency)==**. *(provide scale/scalab - means they can grow in amount better than platform threads)*
## Why Use Virtual Threads?

**==Use *VThreads* in high-throughput concurrent applications, especially those that consist of a great number of concurrent tasks that spend much of their time waiting as [[IO processes]]==**.

>*Server applications are examples of high-throughput applications because they typically handle many client requests that perform blocking I/O operations such as fetching resources.*

## VirtualThread threading model

*VThread* implement **==M:N multithreading==** concept - **==a lot of M user-mode==** threads scheduled to **==few N platform threads==** (previous model was 1:1 -> Java thread = OS platform thread).

*VThread* typically ==**employ a small set of platform threads**== that are used as **_carrier threads_**. Code executing in a *VThread* will usually ==**not be aware of the underlying *carrier thread**==*. 

>*VThreads are implemented similarly to virtual memory. To simulate a lot of memory, an OS maps a large virtual address space to a limited amount of RAM. Similarly, to simulate a lot of threads, the Java runtime maps many VThreads to a small number of OS threads.*

**==Locking==** and *==**IO operations***== are ==**scheduling points where a carrier thread is re-scheduled from *one* *VThread* to another**==. A VThread* may be ==`parked()`==, which ==**disables it from scheduling**==. A `parked()` *VThread* may be ==`unparked()`== which ==**re-enables it for scheduling**==.

[JEP 444](https://openjdk.org/jeps/444):

> There are two scenarios in which a **==virtual thread cannot be unmounted during blocking operations==** because it is pinned to its carrier:
> 
> When it executes code inside a `synchronized` block or method, or  
> When it executes a native method or a foreign function.

## Virtual Thread scheduling

The **==default scheduler==** used to schedule *VThreads* **==is the `ForkJoin` work-stealing scheduler==**, which is well suited to this job.

## Virtual threads and I/O operations

*VThread* are _user-mode_(*platform*) threads scheduled by the *JVM* rather than the *OS*. *VThread* require few resources and a single JVM may support millions of *VThreads*. **==***VThread* are a great choice for executing tasks that spend much of their time blocked==**, often waiting for [[IO processes]] to complete.

> *when code running in a VThread calls a blocking I/O operation, the Java runtime suspends the VThread until it can be resumed. The **==OS thread associated with the suspended VThread is now free==** to perform operations for other VThread*

**==The synchronous networking Java APIs(I/O), when run in a *VThread*, switch the underlying native [[socket]] into non-blocking I/O mode==**. When an I/O operation invoked from Java code does not complete immediately (the native socket returns `EAGAIN` - “not ready” / “would block”), the underlying **==native socket is registered with a JVM-wide event notification mechanism==** (a `Poller`), and the virtual thread is `parked()`. When the underlying I/O operation is ready (an event arrives at the Poller), the virtual thread is `unparked()` and the underlying socket operation is retried.

## Virtual Thread example and call stack

Just call some remote api by url via blocking java I/O - `InputStream.readAllBytes()`.

``` java title:VirtualThreadRemoteApiCall.java
// Tuple of URL and response bytes
record URLData (URL url, byte[] response) { }

List<URLData> retrieveURLs(URL... urls) throws Exception {
  try (var executor = Executors.newVirtualThreadExecutor()) {
    var tasks = Arrays.stream(urls)
            .map(url -> (Callable<URLData>)() -> getURL(url))
            .toList();
    return executor.submit(tasks)
            .filter(Future::isCompletedNormally)
            .map(Future::join)
            .toList();
  }
}

URLData getURL(URL url) throws IOException {
  try (InputStream in = url.openStream()) {
    return new URLData(url, in.readAllBytes());
  }
}
```

```plaintext title:virtual-thread-call-stack
"<unnamed>" #15 virtual
  java.base/java.lang.Continuation.yield(Continuation.java:402)
  java.base/java.lang.VirtualThread.yieldContinuation(VirtualThread.java:367)
  java.base/java.lang.VirtualThread.park(VirtualThread.java:534)
  java.base/java.lang.System$2.parkVirtualThread(System.java:2370)
  java.base/jdk.internal.misc.VirtualThreads.park(VirtualThreads.java:60)
  java.base/sun.nio.ch.NioSocketImpl.park(NioSocketImpl.java:184)
  java.base/sun.nio.ch.NioSocketImpl.park(NioSocketImpl.java:212)
  java.base/sun.nio.ch.NioSocketImpl.implRead(NioSocketImpl.java:320)
  java.base/sun.nio.ch.NioSocketImpl.read(NioSocketImpl.java:356)
  java.base/sun.nio.ch.NioSocketImpl$1.read(NioSocketImpl.java:807)
  java.base/java.net.Socket$SocketInputStream.read(Socket.java:988)
  java.base/java.io.BufferedInputStream.fill(BufferedInputStream.java:255)
  java.base/java.io.BufferedInputStream.read1(BufferedInputStream.java:310)
  java.base/java.io.BufferedInputStream.lockedRead(BufferedInputStream.java:382)
  java.base/java.io.BufferedInputStream.read(BufferedInputStream.java:361)
  java.base/sun.net.www.MeteredStream.read(MeteredStream.java:141)
  java.base/java.io.FilterInputStream.read(FilterInputStream.java:132)
  java.base/sun.net.www.protocol.http.HttpURLConnection$HttpInputStream.read(HttpURLConnection.java:3648)
  java.base/java.io.InputStream.readNBytes(InputStream.java:409)
  java.base/java.io.InputStream.readAllBytes(InputStream.java:346)
  Main.getURL(Main.java:24)
  Main.lambda$retrieveURLs$0(Main.java:13)
  java.base/java.util.concurrent.FutureTask.run(FutureTask.java:268)
  java.base/java.util.concurrent.ThreadExecutor$TaskRunner.run(ThreadExecutor.java:385)
  java.base/java.lang.VirtualThread.run(VirtualThread.java:295)
  java.base/java.lang.VirtualThread$VThreadContinuation.lambda$new$0(VirtualThread.java:172)
  java.base/java.lang.Continuation.enter0(Continuation.java:372)
  java.base/java.lang.Continuation.enter(Continuation.java:365)
```

Looking at the stack frames from bottom up: 
- first, setup of a virtual thread ([[continuation]] are a VM mechanism internally employed by virtual threads), created by the `ExecutorService()`
- second, invoking `retrieveURLs` and `getURL` 
- third, HTTP protocol handler and the `read` method of the *socket* input stream
- finally, virtual thread has been `parked()`, since the server does not send the complete response, so there is not enough data to read off the socket

```plaintext title:read-poller-call-stack
"Read-Poller" #16
  java.base@17-internal/sun.nio.ch.KQueue.poll(Native Method)
  java.base@17-internal/sun.nio.ch.KQueuePoller.poll(KQueuePoller.java:65)
  java.base@17-internal/sun.nio.ch.Poller.poll(Poller.java:195)
  java.base@17-internal/sun.nio.ch.Poller.lambda$startPollerThread$0(Poller.java:65)
  java.base@17-internal/sun.nio.ch.Poller$$Lambda$14/0x00000008010579c0.run(Unknown Source)
  java.base@17-internal/java.lang.Thread.run(Thread.java:1522)
  java.base@17-internal/jdk.internal.misc.InnocuousThread.run(InnocuousThread.java:161)
```

This thread is the JVM-wide `ReadPoller`. At its core **==it performs a basic event loop that monitors all of the synchronous networking `read()`, `connect()`, and `accept()` operations==** that are not immediately ready when invoked in a *VThread*. **==When the I/O operation becomes ready, the `Poller` will be notified and subsequently `unpark()`==** the appropriate `parked()` *VThread*.

>*above stack trace was captured when running the test program on macOS, which is why we see stack frames relating to the poller implementation 
>On macOS, that is [kqueue](https://www.freebsd.org/cgi/man.cgi?query=kqueue&sektion=2). 
>On Linux the poller uses [epoll](https://man7.org/linux/man-pages/man7/epoll.7.html). 
>On Windows [iocp](https://en.wikipedia.org/wiki/Input/output_completion_port) and [wepoll](https://github.com/piscisaureus/wepoll)

- the `Poller` maintains a map of [[file descriptor]] to *VThread*. 
- when a *FD* is registered with the `Poller`, an entry is added to the map for that *FD* along with the registering *VThread* as its value. 
- `Poller`’s *event loop*, when woken up with an event, uses the event’s *FD* to lookup the corresponding *VThread* and `unpark()` it.

## Virtual Threads components



Sources:
- https://inside.java/2021/05/10/networking-io-with-virtual-threads/
- [Loom Proposal](https://cr.openjdk.org/~rpressler/loom/Loom-Proposal.html)
- [State of Loom ](https://cr.openjdk.org/~rpressler/loom/loom/sol1_part1.html)
- [State of Loom 2](https://cr.openjdk.org/~rpressler/loom/loom/sol1_part2.html)
- [Oracle Docs](https://docs.oracle.com/en/java/javase/20/core/virtual-threads.html)



