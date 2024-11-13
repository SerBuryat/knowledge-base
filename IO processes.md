> *In computing, **input/output** (**I/O**) is the communication between an information processing system, such as a computer(OS), and the outside world, such as another computer system, peripherals, or a human operator. 
> Inputs are the signals or data received by the system and outputs are the signals or data sent from it.

I/O ops handled with [[socket]]s and [[file descriptor]]s to perform IO ops.

## I/O models

Examine the basic differences in the five I/O models that are available to us under Unix:

- blocking I/O (basically `read()`/`write()`)
- non-blocking I/O
- I/O multiplexing (`select()`/`poll()`)
- signal-driven I/O (`SIGIO`)
- asynchronous I/O (the [[POSIX]] `aio_*()` functions)

![[io-models.png]]
>*The main difference between the first four models is the first phase: the process is blocked in a call to `recvfrom` while the data is copied from the kernel to the caller's buffer. 
>Asynchronous I/O handles both phases on kernel side.*
### Blocking I/O

In this scenario we make a system call `read()`/`write()` and **==process blocks until data is ready==**.
![[blocking-io-model.png]]

### Non-Blocking I/O

When nonblocking we are telling the kernel "**==when an I/O operation request cannot be completed==** without putting the process to sleep, **==do not put the process to sleep==**, but **==return an 'error'==** instead (**=='not ready yet'==**)".

![[non-blocking-io-model.png]]
> *When an application sits in a loop calling `recvfrom` on a [[file descriptor]] like this, it is called **polling**. The application is continually polling the kernel to see if some operation is ready. This is often a waste of CPU time, but this model is occasionally encountered, normally on systems dedicated to one function.*

### I/O Multiplexing Model

>I/O multiplexing refers to the concept of processing multiple input/output events from a single event loop, with system calls like poll and select (Unix). –[Wikipedia](https://en.wikipedia.org/wiki/Multiplexing)

With **I/O multiplexing**, we call `select()` or `poll()` and **==block in one of these two system calls==**, **==instead of blocking in the actual I/O system call==**.

![[multiplexed-io-model.png]]
> *In multiplexed I/O we put one or more I/O processes: 
> - initial them all 
> - waiting until one or many of them is ready for actual `read()`/`write()`*
> (java `java.nio.channels.Selector` works this way)

### Signal-Driven I/O Model

The signal-driven I/O model uses signals, **==telling the kernel to notify us==** with the `SIGIO` signal **==when the I/O process is ready==**.
![[signal-driven-io-model.png]]
- We first **==enable the socket for signal-driven I/O==** and **==install a signal handler==** using the `sigaction` system call. The return from this system call is immediate and our process continues - it is not blocked.
- When the datagram is ready to be read, the `SIGIO` **==signal is generated for our process==**

The advantage to this model is that **==we are not blocked while waiting==** for the datagram to arrive.

### Asynchronous I/O Model

Asynchronous I/O is defined by the [[POSIX]] specification.
These functions work by **==telling the kernel to start the operation==** and to **==notify us when the entire operation is complete==**. 
The **==main difference==** between this model and the signal-driven I/O model is that with **==signal-driven I/O==**, the kernel tells us **==when an I/O operation can be initiated==**, but with **==asynchronous I/O==**, the kernel tells us **==when an I/O operation is complete==**.
![[asynchronous-io-model.png]]
- We call `aio_read` (the [[POSIX]] asynchronous I/O functions begin with `aio_` or `lio_`) and pass the kernel the following:
    - [[file descriptor]], buffer pointer, buffer size (the same three arguments for `read()`),
    - file offset (similar to `lseek`),
    - how to notify us when the entire operation is complete

This system call **==returns immediately==** and **==our process is not blocked==** while waiting for the I/O to complete - we ask the kernel to **==generate some signal when the operation is complete==**.

## I/O control methods
### Programmed I/O
With programmed I/O, the **==*CPU* executes a program instruction for every I/O data transfer==**. 

### Interrupt-Driven I/O
**==A *CPU* is interrupted when an I/O device needs to transfer data==**. In this case,  **==*CPU*, interprets and executes instructions it receives from a computer program==**. 

### Direct Memory Access (DMA)
This occurs when the **==*CPU* only has to initiate the data transfer==** and the **==*DMA* *controller*(*DMAC*) takes over to complete it==**. 
This control method is more efficient because it requires minimal *CPU* involvement.

>***Direct memory access (DMA)** is a feature of computer systems that allows certain hardware subsystems to **access** main system **memory independently of the CPU**. **Without DMA**, when the **CPU** is using programmed I/O, it is typically **fully occupied for the entire duration of the read or write** operation, and is thus unavailable to perform other work. **With DMA, the CPU first initiates the transfer, then it does other operations** while the transfer is in progress, and it **finally receives an interrupt from the DMA** controller (DMAC) when the operation is done.*

### Advanced Programmable Interrupt Controller (APIC) 
In this method, when the **==*CPU* receives notification that a device is ready to transfer data, it sends a signal to the *APIC*==**. The **==*APIC* then sends an interrupt to the *CPU* so it can execute the program==**.

Source:
- [Input/output](https://en.wikipedia.org/wiki/Input/output)
- [ I/O Multiplexing: The select and poll Functions](https://notes.shichao.io/unp/ch6/#chapter-6-io-multiplexing-the-select-and-poll-functions)
- [I/O Multiplexing (select vs. poll vs. epoll/kqueue)](https://nima101.github.io/io_multiplexing)
- [Asynchronous I/O](https://en.wikipedia.org/wiki/Asynchronous_I/O)
- [asynchronous and non-blocking calls? also between blocking and synchronous](https://stackoverflow.com/questions/2625493/asynchronous-and-non-blocking-calls-also-between-blocking-and-synchronous)
- 