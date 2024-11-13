==Endless non-blocking IO thread==.

*Event loop* is **design pattern** that waits and ==dispatches events or messages== in a program and the ==core mechanism that enables asynchronous operations== 

*Event loop* main task is `polling()` new incoming requests or events and dispatch it to event handlers

![[event-loop.png]]
Here is a simple `SimpleEventLoopExample`:
- 1 - *main thread* to handle user inputs (or requests) and stay responsive
- 15 - *worker threads* to parallel handle events
- *main thread* put events to queue
- *worker threads* take it from queue and handle asynchronously (don't block *main thread*)

``` java title:SimpleEventLoopExample
public class SimpleEventLoop {  
  
    public static void main(String[] args) {  
        var eventQueue = new EventQueue();   
  
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

In this implementation worker threads take events from queue via `while(true){...}` (so called - `pull()`).
But we can change model to `push()`/`notify()`- if worker threads going to `subcribe()` for events - then main thread can `push()`/`notify()` worker threads about incomming event for handling, this helps to reduce CPU work for endless `while` loop (if there are no events for example).