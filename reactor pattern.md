The ==reactor== software ==design pattern== is an event handling strategy that can ==respond to many potential service requests== *concurrently*. 
The pattern's key component is an ==[[event loop]], running in a *single thread* or process, which *demultiplexes* incoming requests and dispatches them to the correct request handler.==
By relying on ==*event-based* mechanisms rather than *blocking I/O* or *multi-threading*==, a reactor can handle many concurrent *I/O bound* requests with minimal delay.

Practical considerations for the *client–server model* in large networks, such as the *C10k problem* for web servers, were the original motivation for the reactor pattern ([[node.js]], [[netty]], etc.)

A naive approach to ==handle service requests from many potential endpoints, such as network [[socket]]s or [[file descriptor]]s==, is to listen for new requests from within an event loop