# Week 5
# Files
## Writing to files
Call ```fopen()``` with the mode ```"w"```. CAUTION: This deletes the contents of the file. Open with ```"a"``` to append. ```fprintf()``` can be used to output to a filestream too.

When a program writes to a stream, it is writing to a region in memory controlled by the OS called a File Buffer, which takes charge of actually writing to the disk.

File IO isn't recommended for debugging as an untimely termination could mean not everything is written to disk.
- The results of the statements are undefined in the event of a crash.
- In the case of abnormal termination, the executed statements may not be written to the disk or the screen.
- They are not reliable functions for debugging.
- It is unknown what the results of the calls will be if your computer loses power in the middle of the program.

```int fflush(FILE *stream)``` can be used to request the OS to write any changes in its buffer.

# Low-Level IO
## Binary files
Ultimately all files are binary data - bytes of 8 bits. Text files contain patterns that are human readable as text.

It can be helpful to store certain program output as binary files, for example if there is no convenient way of storing data as text, like images or audio. Storing data in binary is typically also more versatile and smaller in size than text files, making them useful for when they don't need to be human readable.

They are handled the same way as text files using ```fopen()```. However, they must be opened with a ```b``` appended to the mode, i.e. ```rb``` or ```wb```.

```fscanf(), fgets()``` are not useful for binary files as binary files have no notion of a line.

## Writing binary files
```size_t fwrite(const void *ptr, size_t size, size_t nmemb, FILE *stream);``` to write binary data to a file.
- ```*ptr``` is the data we want to write to the file. Typically the head of an array, but can be the pointer to an individual variable.
- ```size``` is the size of each element to be written.
- ```nmemb``` is the no. of elements to be written to a file.
- ```*stream``` is the file to be written to. Note that the ordering is different, with stream at the end.
- The return value is the number of elements successfully written to the file, or 0 on error.

## Reading binary files
```size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream);``` to write binary data to a file.
- ```*ptr``` is the address we want to write to from the file. Typically the head of an array, but can be the pointer to an individual variable. Note that this is not a constant anymore.
- ```size``` is the size of each element to be read.
- ```nmemb``` is the no. of elements to be read from a file.
- ```*stream``` is the file to be read from. Note that the ordering is different, with stream at the end.
- The return value is the number of elements successfully written to the file, or 0 on error.

Remember that it's important to read data in the same order we wrote it in.

When ```fread()``` returns 0, no data has been successfully read. However, this is not necessarily an error and could just mean the end of the file has been reached.

CAUTION: using ```fread()``` and ```fwrite()``` might be iffy if multiple computers are used, since different systems represent data differently - an effect of endian-ness. When translating data between machines, we must be aware of how computers represent this data internally.

## Reading and writing structs
You could individually read and write the members of a struct, or you could do it in one go with the whole struct. Use ```sizeof()``` since manually computing the size is inconvenient and would eliminate the need to guess the size of a variable; C is also allowed to insert spaces between the members of a struct.

## Moving around in files
Linearly reading a file is useful, but sometimes you need to jump to parts. Every open file contains a current position and that determines where ```fread()``` will read from/```fwrite()``` will write to. Every read/write call changes this position.

```int fseek(FILE *stream, long int offset, int whence);``` is used to jump to positions in a file.
- ```stream``` is the filestream whose position we would like to change.
- ```offset``` indicates how much the file position should change relative to ```whence```. We can move backwards by using a negative offset.
- ```whence``` determines how param 2 is interpreted.

Values of ```whence```:
- ```SEEK_SET``` for relative to the begining of the file
- ```SEEK_CUR``` for relative to the current position of the file
- ```SEEK_END``` for relative to the end of the file

It is particularly important to check for errors in read/write calls after seeking since seeking might set the pointer to an out of bounds index. Failure cases are better caught before or after the seek call.

```fseek()``` is useful and can save memory when you have a very large file that shouldn't be loaded into memory all at once.

```void rewind(FILE *stream)``` can be used to move to the beginning of the file.

# Compiling
## The compiler toolchain
This is the set of tools that turn source code into a running program.

.c source code ```->compile->``` .s assembly ```->assemble->``` .o object files ```->link->``` executable .out ```->run->``` executing program

- A compiler is any program that converts one language into another, typically higher level into lower level.
    Multiple stages:
    -  Front end: lexical analysis <-> syntax analysis, forms an abstract syntax tree in an intermediate language (GIMPL and GENERIC for C).
    - Middle end: Performs semantic analysis on the AST to optimize code and results in a modified AST.
    - Back end: Code generation step converts the AST into assembly code.

- The assembler can be invoked directly with the ```as``` command. This creates .o object files which are not human readable anymore. However, this is not enough to execute the code.
(```gcc -S``` can convert C code to assembly code and ```gcc -c``` can convert C code into object files.)

- calling gcc compiles the source code, invokes the assembler and then links the compiled and assembled .o files together to form a final executable.

- Linking takes multiple object files and bundles them into one executable file. The final executable file is a package that contains all of the instructions in the programs as well as a data section containing constants and links to dynamic libraries. These libraries contain object code for functions such as ```printf()```.

Everything from the assembly code to the object files to the executable are built for a specific type of machine. The executable is NOT portable and is specific to the type of the machine, OS and sometimes even system configuration.

To execute the .out file, an OS specific loader loads the program into memory.

Finally, before even starting the compiler, a pre-processor is needed to prepare the source code for the compiler front end. It contains library and function prototypes required for the program to run and directives like macros.

## Header files
Complex programs typically have some includes, user defined types and various function prototypes. Sometimes, these functions are implemented in other files. Compiling this file then raises linking errors since the implementations of the function prototypes are in another file. To solve this, we can list all of the files that contain the code.

When multiple files are part of a larger program, each file is compiled and assembled separately. 

Then, all the of these object files are linked to form an executable.

We can also carry out this process separately in gcc as
```
> gcc -c file1.c
> gcc -c file2.c
> gcc file1.o file2.o
```

This is called separate compilations and has the advantages that not every file needs to be recompiled when a change is made to one file. We can just compile the new file and link it together. This can save a lot of time for huge projects.

However, this could result in issues where for example changing the parameter types of a function implementation results in a mismatch with the prototype defined in another file. This arises since the compiler doesn't cross-reference the files. Header files fix issues like these while still giving us the benefits of separate compilation.

A header file (.h) is an interface and declares the design while the corresponding .c files implement them.

Header files are also useful if you want to use functions in multiple files; without them, we would have to copy function protoypes to every file that needs them.

Header files can also contain type definitions.

Header files are used by including ```#include "header.h"``` in the file that needs to use it. The quotes tell the compiler that the header file is in the current directory while the <> tell the compiler to look in the system library.

```#include "header.h"``` must also be present in the .c file that implements the header file's design.

We do not specify the header file to the compiler since the ```#include``` statement basically copies over the header file's contents before compiling begins.

## Header file variables
Creating variables inside a header file can result in variable duplication linker errors. This is because the same variable is copied over to every file that uses it. We need to separate the definition and declaration of these variable types.

We shift the definition of these variables into the relevant .c file and add the ``extern`` keyword in front of the variable declaration in the header file.

What if we want a global variable in every file and it happens to share the same name as a global variable in another file? We don't want it declared in a header file since it is not a part of the design, so we use the ``static`` keyword to make the variable local to the file that defined it.

``static`` is used differently if used in conjunction with a local variable. In this case, it means that the variable should keep its value across function executions.

We frequently want to include our header file in multiple files but that might again result in duplicate symbol errors. We can add a guard statement to prevent this:
```
#ifndef HEADER_H
#define HEADER_H

// header file contents

#endif // HEADER_H
```
Read ```ifndef``` as "if not defined". When this header file is met the first time, the constant ``HEADER_H`` will be defined. When the header file is included next, the condition will be true and the contents of the header file will not be processed.

The source files must be recompiled if the header file changes, even if the source remains unchanged. This is because of a dependency on the header file.

The compiler toolchain includes the tool ``make`` to keep track of all of these dependencies.

## Makefiles
Makefiles are composed of a sequence of rules:
```
target: dedpendencies...
    recipe
```
IMPORTANT NOTE: the whitespace before recipe is a TAB, not a SPACE.

Each rule has:
- a target, which is a file to be constructed
- a recipe, the command(s) to execute to create the target and
- dependencies; if one or more of the dependencies is newer than the target, the recipe is executed. If there are no dependencies, the actions are only executed if the target doesn't exist.

The default name is ```Makefile```. Just execute ``make`` to find and execute the makefile.

Matches:
- ``%`` as a wildcard
- ``$<`` for the first dependency
- ``$@`` for the target name

``.PHONY: target`` indicates that ``target`` is not an actual file, but just a legal target.

Makefiles can also contain variables which can be subbed in as ``$(VAR_NAME)``.
