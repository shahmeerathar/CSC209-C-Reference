# Week 8
# Pipes
Use ``strace`` on Linux to see which system calls are being made.

File descriptors are integers that represent open files or communication channels. Typically 3 because 3 files are opened for you for ``write``: ``stdin, stdout`` and ``stderr``

A pipe is a data structure to be used to send data between processes. A pipe has two file descriptors: one for writing to the pipe, and one for reading from the pipe.

``fork`` also makes a copy of all open file descriptors, which the child process inherits.

Use ``dup2`` to copy file descriptors.

# Week 9
# Signals
``ctrl-c`` kills a running process, ``ctrl-z`` suspends a running process, ``fg`` resumes a suspended process. A segfault is also a signal.

Signals allow the OS to interrupt a currently running process and notify it about an event that has occurred.

Signals are identified by a number from 1 to 31 and define constants are used to identify them. Each signal is associated with a default action.

We can use the cmd line program or the library `kill` to send signals:
```
kill SIGNAL pid
```

## Changing the default state of a signal
Used to print messages, ignore the signal or save a state etc.

The PCB contains a signal table, similar to the open file table, containing a pointer to the code (signal handling function) to be executed when this signal is called. ``sigaction()`` can install a new signal handling function.

We must initialize a new struct before calling ``sigaction()`` containing a funciton pointer to the new signal handling function.

Unless `exit()` is called, the signal handling function hands control back to the process where it was interrupted.

`SIGKILL` and `SIGSTOP` are immutable.