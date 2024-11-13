Fiber is a **==lightweight user-mode thread==**.

> *fibers implemented as computer program component between user space and OS platform threads*

Fibers are instead a ==**form of cooperative multitasking**== ([[thread#Thread models]]). It means that it is our responsibility for the fibers to co-operate with each other.

Fiber has ==**external mechanism of suspending/rerunning**== code as a scheduler.
(instead [[coroutine]], it hasn't )

Source:
- [[fibers and coroutines.pdf]]