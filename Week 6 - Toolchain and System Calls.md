# Week 6
# Useful C Features
## typedef
Allows you to define an alias for a type that is evaluated at compile time.

Doesn't create a new composite type; you're providing a new name for an existing type.

You could give the same type multiple typedefs, making them uniquely recognizable in specific contexts.

A downside is that sometimes it's not completely clear how to use a typedef alias, and you can pass values of one typedef alias into another if they have the same underlying type.

typedef does make using structs easier, since you can get rid of having to use the struct keyword every time.

Convention: structs are named in lowercase and their typedefs have an uppercase first letter.

```
typedef struct name {

} Name;
```

We can sometimes even omit ``name``, though it might be useful to keep it.

## Macros
Used as aliases for something other than types. Quite powerful and often used to evaluate simple expressions or constants.

```
#define MACRO_NAME value
```

``value`` is substituted into every reference to ``MACRO_NAME`` by the preprocessor, before compilation.

The macro language is NOT C and doesn't require ``=`` or ``;``.

Can also take parameters:
```
#define MACRO_NAME(parameter) (param expression)
```

This is like a little function, but happens before compilation so it's efficient.

Gotchas:
- Can't have any space before the macro name and opening parantheses.
- Parameters and definition must be in parantheses to ensure proper operator precedence functioning.

# The C Preprocessor
# Simple macros and header files
Every expression beginning with a ``#`` is a preprocessor directive.

``#define`` associates a value with a string name. Strong convention: all uppercase name.

``cpp *.c`` invokes the preprocessor which expands all the macros and executes all the directives, creating a stream of tokens passed to the compiler's parser.

The preprocessor replaces every found macro name text in the code with the TEXT value (even inside comments). The exception is patterns found within quotes, which are not replaced. It also only looks for complete tokens.

Macros can also be nested.

Pre-defined macros common across all systems provide general info about compilation:
``__LINE__, __FILE__, __DATE__, __TIME__``
System provided macros are enclosed in double underscores. There's also difference macros per system, i.e. Linux or Windows. Useful if you want code to behave differently depending on the system it's being run on.

Can't be used to print informational messages; conditionals must be used encapsulating C variables. Use the tokens ``#if #elif #else #endif #ifdef #defined``. Each block in a sequence of conditionals needs to be terminated by another directive.

We can also define macros at compile time with the ``-D`` tag. By default, macros are defined with value 1 but they can be defined with different values. We can combine this with make to quickly build a bunch of different compile sequences.

Careful - macros can be easily abused.

The preprocessor is also useful because it creates one single file using the ``#include`` directives that is then passed into the compiler, which can only compile one file at a time.

## Function-like macros
Differs depending on system, so not recommended.

Use ``SET`` to set bit values and ``ISSET`` to check.

Although a macro looks like a function and we expect it to act like one, there are a few gotchas. For example, passing an expression in as a parameter causes the expression to be evaluated twice.

Mscros are faster than C functions since they are evaluated before compile time. However, C can declare static inline functions which removes that discrepancy.

A weekness and strength: macros are type neutral.

Compilers also don't include macros in their debug info.

## Function pointers
We can declare pointers to functions as data like so:
```
func_return *func_ptr_name(func_params) = func_name;
```
Note that there is no need to dereference the pointer when calling its assocaiated function.

This is helpful in cases where we would want to call various functions of the same signature without having to recompile our program every time. We can combine this with an array of structs that contain function names and pointers, which we can then iterate over.

Syntax for a function that returns a function pointer:

```
func_ptr_return_type (*func_to_call())(func_ptr_params)
```
We can use typedef to clean up this messy syntax too by declaring an alias for the relevant function pointer.

# System Calls
Functions that request a service from the OS.
- ``void exit(int status);`` is a request to terminate the program. The OS executes instructions to clean up data structures representing the running process.
- ``read()`` and ``write()`` are low level system calls that are used by the higher level library functions ``scanf()`` and ``printf()``, which are not themselves system calls; rather, they do a bunch of other work and call the relevant system calls.

When a system call is made, control is passed to the OS.

Both system and library calls act like functions so there isn't a huge difference from a user perspective.

Each OS has a list of supported system calls. Broadly divided on Linux into:
- File management
- Process management
- Interprocess communication calls

## Errors and Errno
It is possible for system calls to fail since they interact with external resources over which we have no control.

Thus it's important to check if system calls have succeeded or failed before proceeding.

Most system calls return a special value to indicate an error or success.

For integer returns, -1 = failure and NULL = failure for pointer returns.

A global variable ``errno`` is used to store error types.

``void perror(const char *s);`` automatically writes the corresponding error string in ``errno`` to ``stderr``.

Invoke ``exit()`` when you need to terminate due to a fatal error.