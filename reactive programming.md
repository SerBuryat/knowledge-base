>*react when some event happend*

Reactive programming is a declarative **==programming paradigm that is based on the idea of *asynchronous* event processing==**. *Async* processing **==means that the processing of an event does not block==** the processing of other events (or other threads).

>*In the GUI applications, the user interface was updated only after some user action, like clicking a button. The heart of the application was a so-called event loop. It was an endless loop and was responsible for handling user input and updating the user interface. But as the program was waiting synchronously (i.e., blocking) for the user input, nothing else could happen. And waiting synchronously for an event that doesn’t come means that the whole application hangs and the GUI becomes unresponsive.
>
>*The solution is to decouple the event handling from the GUI update. The decoupling is done by introducing an event queue and parallel event processing. This approach is well known as the producer and consumer pattern. A thread handling user input puts events into the queue, and a consumer thread takes events from the queue’s head and processes them. The consumer thread may also act as a producer by putting GUI update requests into a queue processed by a GUI update thread. Such producer/consumer chaining then leads to reactive streams.*

Here is a simpe `SimpleEventLoopExample`:
- 1 - *main thread* to handle inputs(or requests) and stay responsive
- 15 - *worker threads* to handle events
- *main thread* put events to queue
- *worker threads* take it from queue and handle asynchronously (don't block *main thread*)

``` java title:SimpleEventLoopExample
public class SimpleEventLoop {  
  
    public static void main(String[] args) {  
        var eventQueue = new EventQueue();  
  
        // add some task for handling from start  
        for(int i = 0; i <= 500; i++) {  
            eventQueue.add(new Event("event " + i));  
        }  
  
        // threads = CPU cores  
        var executor = Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors());  
  
        // workers threads = CPU cores - 1 (1 for EventLoop)  
        var workersCount = Runtime.getRuntime().availableProcessors() - 1;  
  
        for (int i = 1; i <= workersCount; i++) {  
            System.out.println("Executor " + i + " started!");  
            executor.submit(() -> new EventHandler(eventQueue).handleEvents());  
        }  
  
        // run EventLoop in dedicated thread  
        executor.submit(() -> new EventLoop(eventQueue).acceptEvents());  
    }  
  
    private record Event(String event) {}  
  
    private static class EventQueue {  
  
        private final LinkedBlockingQueue<Event> eventQueue = new LinkedBlockingQueue<>();  
  
        public void add(Event e) {  
            eventQueue.add(e);  
        }  
  
        public Event next() {  
            return eventQueue.poll();  
        }  
  
    }  
  
    // just take an event from queue and handle `HANDLING_TASK_MS` time  
    private static class EventHandler {  
  
        private static final int HANDLING_TASK_MS = 1000;  
  
        private final EventQueue eventQueue;  
  
        public EventHandler(EventQueue eventQueue) {  
            this.eventQueue = eventQueue;  
        }  
  
        public void handleEvents() {  
        // we can change this part from pull() model to push()  
		// instead endless check new event exists  
		// just notify event handler via Observer pattern
            while (true) {  
                var event = eventQueue.next();  
  
                if(event != null) {  
                    try {  
                        Thread.sleep(HANDLING_TASK_MS);  
                    } catch (InterruptedException e) {  
                        throw new RuntimeException(e);  
                    }  
                    System.out.printf(  
                            "'%s' event handled with '%s' thread%n", event, Thread.currentThread().getName()  
                    );  
                }  
            }  
        }  
  
    }  
  
    // accept events from user input via Scanner  
    private static class EventLoop {  
  
        private final EventQueue eventQueue;  
        private final Scanner scanner = new Scanner(System.in);  
  
        public EventLoop(EventQueue eventQueue) {  
            this.eventQueue = eventQueue;  
        }  
  
        public void acceptEvents() {  
            while (true) {  
                System.out.println("Waiting event...");  
                var event = new Event(scanner.next());  
                eventQueue.add(event);  
                System.out.println(event + " accepted and added to event queue");  
            }  
        }  
  
    }  
  
}
```

> *In this implementation worker threads take events from queue via `while(true){...}` (so called - `pull()`).
> But we can change model to `push()`/`notify()`- if worker threads going to `subcribe()` for events - then main thread can `push()`/`notify()` worker threads about incomming event for handling, this helps to reduce CPU work for endless `while` loop (if there are no events for example)
## Observer Pattern

Reactive systems make use of *async* communication. Caller registers a *callback procedure* that is called when the result is available. **==The caller can continue his work, and the *callback* procedure is called when the result is available==**. (see [[CPS]])

The *callback pattern* is usually implemented in *OOP* using the *Observer* pattern. **==In the *Observer* pattern, the return value of the async procedure call is called the _Observable_, and the callback procedure is called the *Observer*==**:

![[rx-observer-pattern.webp]]

From the programmer’s view, a code making use of the *callback* or **==*Observer* pattern may be hard to read==**. And it even gets worse when the *callback procedure* calls another asynchronous procedure. In this case, the *callback procedure* is called when the result of the first procedure is available and calls the second procedure. **==The second procedure then registers its callback procedure, and so on. This is apply called the *callback hell*==**.

To handle *callback hell* several approaches exist:
- Promise/Future
- async/await
- RxJava, Reactor and other libraries to unite CPS functions in a chain

## Reactive Streams

In the case of **==reactive streams, the *Observable* is not just a single event but an event stream==** (simply `List<Observable>`). The *Observer* then has to provide “callbacks” for success and failure and for the end of the stream.

Reactive streams may also **==differ in how the stream flow is controlled. There are two approaches: `push()` and `pull()`==**. In the **==`push()` approach, the consumer doesn’t know exactly when a new event is emitted==**. In this case, the **==consumer may easily be overwhelmed by the stream of events==**. So, the consumer has to be able to control the stream flow somehow. This is called back pressure. In the **==`pull()` approach, the consumer explicitly decides when to get the next event from the stream==**.

Source:
- [What Is Reactive Programming?](https://www.baeldung.com/cs/reactive-programming)