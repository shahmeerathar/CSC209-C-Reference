# Weeks 1 and 2
# IO and Compilation

    #include <stdio.h> // library for IO
    #define MY_CONSTANT 10; // for defining global constants

    printf("Output string, %formatSpecifier", variableToOutput); // for outputting to console
    scanf("%formatSpecifier", &variableToStore); // for inputting data from console

## Format specifiers

    %d // signed decimal integers
    %i // integer
    %c // char
    %f // float
    %p // pointer

## Compilation

    gcc -Wall -o outputFileName source.c
    # -Wall reports more in-depth warnings/errors
    # -o lets you specify output file name

## Terminal
    
    ls # to list files in current directory
    ls -F # highlights executables with an asterisk
    ls -l # lists file names with permissions

# Arrays

    type arrayName[lengthOfArray]; // stored as contiguous blocks of memory

Indexing starts at 0, index refers to offset from starting position.

Address of ```A[i]``` is the same as:

- The address of ```A + (i * size of A[0])``` bytes
- The address of ```A[0] + (i * size of a single element of A)``` bytes

For an array of X elements, writing A[Y] where Y >= X could give you a seg fault, just appear to work fine, or give you an error if you're lucky.

# Pointers
## Introduction

A pointer is a variable that holds a memory address and is a powerful concept because you can directly access and modify memory locations.

    int i = 10;
    printf("%p\n", &i); // & operator gets the memory address of a variable

- If a variable is declared a pointer, its type is a memory address.

- Declared with the * operator.

In a 64-bit system, 32 bits are allocated for the memory address where the pointer itself is stored and 32 bits are allocated for the memory address the pointer stores.

    int *pt; // of type int* or pointer to int
    pt = &i; // we say that pt points to i

When we declare a pointer, it is important to remember that we allocated space for the pointer itself, not the data type that the pointer points to.

The * operator dereferences, or returns the value pointed at by the pointer.

    printf("Value pointed to by pt: %d\n", *pt);

Using * inside a declaration is NOT a dereference, i.e. int *pt. It serves as a dereference when used in an expression, i.e. in the last printf function call.

## Assigning to dereferenced pointers

    int i = 7;
    int *ptr = &i;
    *pt = 9;
    // Sets the value of i to 9. i and *pt are aliases; they point to the same memory location.

## Another variable

    int i = 7;
    int j = i;
    int *ptr = &i;
    *pt = 9;
    // Although j is set to the value of i, i and j are at two different memory addresses and thus are not aliases, so j remains unmodified and still holds the value 7.

## The * operator on both sides of the assignment operator

    *pt = *pt + 1; \\ increments the value stored in *pt

## A common gotcha

    int *pt = q;

refers to

    int *pt;
    pt = q;

and not

    int *pt;
    *pt = q;

This is because the * in the declaration is a part of the type, even though by convention it is in front of the variable name.

## Naming convention

    int *p;
    int * p;
    int* p
    // all three are equivalent but the convention is to follow the first style.

## Pointers as function parameters
Function parameters are local variables in the stack frame of their corresponding function and are assigned the value of the calling argument.

Thus, if you pass in a variable to a function, a local variable inside the function will be assigned the passed-in value and stored in a separate memory address.

Hence, any mutations to this local variable will have no effect outside the function. We can use return values to deal with this, but that could be finnicky.

Using pointers to fix this:

    void incrementer(int *ptr) { //parameter declared as a pointer to an int
        (*ptr)++; //parameter pointer dereferenced
        \\ parantheses used to force dereference before increment
    }

    void main() {
        int i = 10;
        incrementer(&i); \\ address of i passed into incrementer()
        return 0;
    }

## Passing arrays as parameters
We generally want to pass pointers and not arrays.

As an example, param ```int A[4]``` is misleading because we are not actually passing in an array of size 4 but a pointer to the start of the array. We should use ```int *A``` as the parameter.

If we want to loop over the elements in an array of an arbitrary size that is passed into another function as the head pointer, we would have to pass in the size of the array as another parameter. This is because using ```sizeof()``` on the parameter defined as ```int *A``` would give you the size of the address stored in the pointer, and not the size of the array itself!

All this has the result that mutating a passed-in array in a helper function makes these mutations visible in the calling function too.

A helper function which has the pointer to an array passed into it CAN change the values in the array but NOT the address of the array. This is because the pointer passed into the helper function is stored as a local variable in said helper function.

## Pointer Arithmetic
The basic operators ```+, /``` etc. act differently depending on the types of the operands.

Adding an integer ```i``` to a pointer increases the hex address of the pointer by ```i * size of the type of variable the pointer references```. I.e., a pointer to an int will increase by 4 bytes if incremented since an int takes 4 bytes, Simiilarly, a pointer to a char will increase by 1 byte.

This is designed this way because of arrays. This allows another way of iterating over arrays where you can just increment the pointer to the array to get the next index, i.e. ```*(ptr + 1)``` is equivalent to ```A[1]``` where ```ptr``` is the pointer to the array ```A```.

Pointers can also be 'incremented' with array style indexing. ```ptr[1]``` is equivalent to ```*(ptr + 1)```. This refers back to the way C evaluates the address of ```A[i]```.

## Pointers to pointers
We can store the address of a pointer in another pointer variable.

A pointer to a pointer to an int for example is ```int **pt_ptr```. We can also dereference twice by ```**pt_ptr```. For this to be legal, ```pt_ptr``` MUST be a pointer to a pointer to a type. This can extend practically infinitely and the number of prefix stars increases with every layer.

This is useful for cases such as matrices. Passing in arrays of pointers is also very common.