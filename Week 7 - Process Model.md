# Week 7
## Process Models
Program: The executable instructions of a program, either source code or machine code.

Process: Running instance of a program. Includes machine code + the current state of the process, such as current variable values or what instruction to execute next.

Represents the state of the program (increasing addresses):
```
Code -> Globals -> Heap -> Unused -> Stack -> OS
```

A Process Control Block is associated with every process, which the OS keeps track of. This includes the PID (Process Identifier), the stack pointer that points to the top of stack memory and the program counter which identifies the next instruction to be executed.

Number of active processes > number of processors currently executing instructions (depends on number of cores)

Three process states:
- Running (currently executing)
- Ready (could be executing if a processor was available)
- Blocked (waiting for another event to occur)

The OS maintains the illusion of multi tasking by rapidly switching between ready and running. Managed by the OS scheduler and we can abstract this detail away.

## Creating processes with fork
You can make a system call to ``fork`` to set up a new process, for which the OS sets up the relevant data structures like the PCB. Calling ``fork`` makes the process pass control to the OS.

``fork`` duplicates the calling process so the new PCB has all the same counters and values except the PID, which is new and unique.

Original process: parent process.
New process: child process.

The child process starts executing right after ``fork`` returns. However, either of the parent or child processes could execute first, depending on the OS scheduler.

``fork`` returns the PID of the child process if successful , but it might fail if there are too many running processes. In this case, it returns -1. After the call to ``fork`` returns, the parent and child processes are now separate and do not share memory.

In our code, we can execute different instructions depending on the value returned by ``fork``.

## Process relationship and termination
The output for a specific process will be in the expected order. However, the output of multiple child processes will interleave in an unpredictable way.

From the OS's POV, there are no real distinctions between child and parent processes so they are treated the same when it comes to scheduling.

The shell is also just another process managed by the OS so shell output might be printed before multiple child processes are done executing (since the shell outputs as soon as the parent process is done executing) even though it looks like the program might have hung up.

``usleep`` can be used to space out time between instructions.

## Running different programs
The ``wait`` system call can be used to make a parent process wait until one of its children have terminated. ``wait`` takes in a pointer to an int that sets the terminate status of the child block and returns the PID of the terminated block.

Bits in the status argument are divided up and used for various purposes:
- The lowest 8 bits in particular tell us whether the process terminated normally or received a signal and which specific signal.
- The exit value of the child process is in the next 8 bits.

``wait`` defined various macros to extract specific bit groups:
- ``WIFEXITED`` for if the process terminated properly or not
- ``WEXITSTATUS`` for process exit value
- ``WIFSIGNALED`` for if the process terminated with a signal
- ``WTERMSIG`` for signal value

``waitpid`` lets you specify which child process to wait for. ``WNOHANG`` can be used if the parent wants to check if a child process has terminated but doesn't want to block.

``wait`` and ``waitpid`` only allow us to deal with child processes and not unrelated or even grandchildren processes, so synchronization is relatively limited.

## Zombies and orphans
The OS can't delete the PCB of a child process until it knows it's safe to be cleaned up because the exit value of the child process might be required by the parent. Thus, child processes that exit are stored as zombie processes.

When the parent of a child process terminates before the child, the child process becomes an orphan, then is 'adopted' by init (PID = 1), the first process that an OS launches.

A zombie process is 'exorcised' and cleaned up when its termination status has been collected. This is done by calling ``wait`` in a loop for every child process.

## Running other programs
The ``exec`` family of functions can launch external processes. ``execl`` takes the path to the other executable and the command line args as params.

Calling ``execl`` causes control to pass to the operating system and the calling function's process control block is modified to encompass the executable being executed.

The OS loads the new executable into memory where the code segment is, initialises a new stack and updated the PC and SP.

Notice that since new instructions are loaded in, no line in the original program after the ``exec`` call will run since they have been overwritten, unless `exec` fails, so error checking is still important.

``exec`` suffixes:
- `l` takes in command line arguments as a list
- `v` takes in command line arguments as an array os strings
- `p` takes in the PATH variable where the executable is held.
- `e` specifies other environment variables.

The shell is just another process that calls ``fork`` to create a new process then ``exec`` to load the executable. It then calls ``wait`` to wait for the child process to finish executing.