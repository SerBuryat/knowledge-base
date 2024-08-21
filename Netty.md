*Netty* — an ==**asynchronous event-driven== network** application **framework**.

>*The main purpose of Netty is building high-performance protocol servers based on java NIO and since 4.0.0 version with java NIO 2*

Instead of *Tomcat* (*thread-per-request* and *blocking I/O* model) , *Netty* using *non-blocking I/O* via `EventLoop` model where 1 (or n-threads == CPU cores) handling all requests.

