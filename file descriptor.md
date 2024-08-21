In *Unix* and *Unix-like* OSs, a file descriptor (*FD*, less frequently *fildes*) is a **==process-unique identifier  for a file or other *I/O resource*, such as a network [[socket]]==**.

>*File descriptor is a part of the [[POSIX]] API*

In the traditional implementation of Unix, *FD* index into a per-process ***FD* table maintained by the kernel**, that in turn indexes into a system-wide **table of files opened by all processes**, called the **file table**. This table records the _mode_ with which the file (or other resource) has been opened: for reading, writing, appending, and possibly other modes. ==**To perform input or output, the process passes the FD to the kernel through a *system call***==, and the **kernel will access the file on behalf of the process**. **==The process does not have direct access to the file==**.

> *"The process does not have direct access to the file…" - means process may not wait for I/O operation complition and just check FD status in file table - gives us a possibility for non-blocking I/O* 

Source: 
- https://en.wikipedia.org/wiki/File_descriptor