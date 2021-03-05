# Week 8
# Pipes
Use ``strace`` on Linux to see which system calls are being made.

File descriptors are integers that represent open files or communication channels. Typically 3 because 3 files are opened for you for ``write``: ``stdin, stdout`` and ``stderr``

A pipe is a data structure to be used to send data between processes. A pipe has two file descriptors: one for writing to the pipe, and one for reading from the pipe.

``fork`` also makes a copy of all open file descriptors, which the child process inherits.

Use ``dup2`` to copy file descriptors.