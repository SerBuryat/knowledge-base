Running program starts a process *(start program == start process)*.
Each **==process has separate memory access==**(except [[thread]]s - they sharing memory).

## Processes bounds

Processes in OS can be divided on 
- CPU bound
- I/O bound
- memory bound

### CPU bound

Process reqiure more CPU time to run instructions.
![[CPU bound.png]]
> *more CPU speed -> faster process completion*
### I/O bound

Process spend more time for waiting I/O(blocking I/O) ops to be done.
![[IO bound.png]]
> *all ops outside CPU: network, hdd, usb, keyboard etc. *

### Memory bound

Process requires mostly RAM(or memory).
> *Every CPU bound code has variables and variables keeps in RAM (stack or heap).
> So can we divide CPU and RAM?*
> *May be some data keeps in L1/L2/L3 CPU cache?*

## Procces execution model

- **Synchronous** is defined as happening at the same time (in predictable timing, or in predictable ordering);
- **Asynchronous** is defined as not happening at the same time. (with unpredictable timing or with unpredictable ordering);
- **Blocking** is when you simply choose to wait for the other thing to be done and return you a response before moving on to the operation that needed it
- **Non-blocking** is when you choose to do other unrelated things while you wait for the operation to be done. Checking back on the availability of the response as you see fit

|             | synchronous                                                                                              | asynchonous                                                                               |
| ----------- | -------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| *block*     | - block must be a synchronus, becuase it has to be executed in order<br>- Synchronous might not be block | Not exist                                                                                 |
| *non-block* | Non-block and Synchronous at the same time is polling/multiplexing                                       | Non-block and Asynchronous at the same time is parallel execution, such as signal trigger |
(relative to [[IO processes]])

>*It's good to understand that while non-blocking and async (or what I prefer to call evented) do allow you to do other things while you wait, you don't have too. You can choose to constantly loop on checking the status of a non-blocking call, doing nothing else. That's often worse than blocking though (like looking at the toaster until it's done), so a lot of non-blocking APIs allow you to transition into a blocking mode from it. For evented, you can just wait idle until you are notified. The downside in that case is that adding the notification was complex and potentially costly to begin with (you had to buy a new toaster with beep functionality, or convince your partner to watch it for you).*

