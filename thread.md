
Is the ==**smallest execution== sequnce (unit)** in **[[OS process]] ==managed by OS thread scheduler**==.

> *It's fair to think about thread as an OS subproccess*

## Multithreading

**==OS process divided with threads==** and gives **==CPU time for every thread (time slices)==** to do it's job.
**==OS use interruptions to stop==** current thread execution and switch to another.
OS switches thread if **==time is up==** or **==thread blocks==** for I/O.
**==Threads sharing memory and recources==** within process.
Threads have ==***common* heap**== and ==***separated* stacks**==.

### Thread models

- **==preemptive==** - OS or runtime decide when to switching threads
> *this means that all of the usual issues of threading, where we don’t know anything about the order of our threads executing*

- **==cooperative(non-preemptive)==** - the thread release and gives time to another threads
>*running thread will continue to run until it signals that it can yield to another*
### Concurrency 

Means **=="dirty parallelism"==** - multiple threads execution **==on single CPU==**.
Thread can be **==interrupted(stopped) in middle of his execution==** by another thread to run.
> *For human sight even concurrency looks like "at the same time" cause of realy fast CPU threads switching (4ghz CPU speed = 4B ops per sec)*

### Parallelism

Means **=="true parallelism"==** *(unlike "Concurrency")* is possible **==only for multicore CPU==** - threads possible to **==run at the same time==** on different CPUs *(physical or virtual via [[hypethreading]])*.

### Requierments and problems

- threads **==resource managment==**: memory, CPU time and **==context switching==**
> *~1MB RAM per thread and time to switching between threads*
- **==race condition - the "fight" for sharing resources==** (*threads synchronization*)
> *non concurrent data structures losses cause "thread stopes in the middle" by thread scheduler*
- **==deadlock==**- **==waiting==** for another thread **==release shared resource==**
> *infinite mutual threads waiting*
- **==perfomance bottlenecks==** 
> *(context switching + time sliced threads) time > (1 thread whole task job) time (in 1 core CPU)*;
> *locking(mutex) critical code area for only 1 or few threads at the same time*;