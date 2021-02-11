# Week 3
# C Memory Model and Dynamic Memory Allocation
Local variables declared in stack - current function always on top.

Dynamically allocated memory in the heap.

Currently running code in the code block; address close to, but not, 0.

Highest addresses reserved for OS.

Cannot return a pointer from a function since it is declared in stack memory; the program would give a warning and might appear to work but there is a problem. Returning the function deallocates that memory on the stack and accessing it could end up giving you a random garbage value.

The heap is then used to allocate memory for variables that need to persist after the return statement and remains until the programmer explicitly deallocates it.

## malloc()
```void *malloc(size_t size); ``` is used to allocate memory on the heap.

Returns a void pointer since malloc doesn't know what the memory will be used for and because it is used to allocate memory for a bunch of different data types. 

```size_t``` is an unsigned int and is important to ensure proper array arithmetic and the use of the pointer as an array.

## free()
Imagine allocating memory in a function with ```malloc()``` and storing it to a pointer in the stack. When the function ends, unless the pointer is returned and assigned to another space in the stack, the pointer to the allocated memory will be lost. However, the original allocated memory will still remain in the heap.

This is bad since it is now just taking up space and is inaccessible, resulting in a memory leak. This can add up in bigger programs. If we run out of space due to memory wastage, we we will receive an ```ENOMEM``` error. Thus, we must explicitly deallocate this memory. We can do this by calling ```void free(void *ptr);``` We don't have to explicitly specify the size to deallocate; it will deallocate all the memory assigned by that ```malloc()``` call.

Note that ```free()``` does not change the address pointed to by *pt and so you can still dereference it. ```free()``` does NOT reset the value of *pt. neither does it change the value stored in the previously allocated memory; it merely specifies that this block is now available for other purposes. However, you should NOT access freed memory since it creates unsafe dangling pointers that may or may not point to the correct address.

## Returning addresses with pointers
We can set the function return type to be ```type *``` and return the pointer that is assigned the return value of ```malloc()```. For example:

```
int *helper() {
    int *arr = malloc(sizeof(int) * 2);
    arr[0] = 1
    arr[1] = 2
    return arr
}

int main() {
    int *data = helper();
}
```

We can also use them through parameters; this frees up our return type for other purposes. First a common error:

```
void helper(int *arr) {
    arr = malloc(sizeof(int) * 2);
    arr[0] = 1
    arr[1] = 2
    return arr
}

int main() {
    int *data;
    helper(&data);
    free(data); // Call after finished using
}
```

This will not work as ```arr``` in ```helper()``` is a local variable defined on the stack. We can fix this by using double pointers:

```
void helper(int **arr_ptr) {
    *arr_ptr = malloc(sizeof(int) * 2);

    int *arr = *arr_ptr // For convenience and must be put after malloc()
    arr[0] = 1
    arr[1] = 2
    return arr
}

int main() {
    int *data;
    helper(&data);
    free(data); // Call after finished using
}
```

## Nested data structures
We can nest two layers of memory allocation - however, when freeing, we must free the sub elements first otherwise dangling pointers will be created.

Good practice is to write the free statement when you write the malloc statement so you don't forget.

# Command-line Arguments
## Strings to integers
We can declare string as ```char *s = "String";```

Sometimes we want to convert strings that store numbers into integers. There is a built in function ```long int strtol(const char *str, char **endptr, int base)``` that returns a long int, which we can assign to an int without losing information.

```strtol()``` has a third and last parameter that you can use to specify the base too.

It can also handle leading spaces and negative signs but trailing whitespaces are considered unconverted.

We can set a pointer to a char and pass in its address as the second parameter. Then, this pointer is set to point to the leftover value that ```strotl()``` does not convert.

## Command-line arguments
The main function can also have the form ```int main(int argc, char **argv)```.

```argc``` is the number of command line arguments and ```argv``` is the string of arguments. Position 0 of ```argv``` is the name of the executable.

One major use of command line arguments is to pass in arguments that change the behaviour of the program, known as a flag.

We should also ensure that the program was called correctly by checking ```argc``` and ```argv```. If the user inputs arguments of the wrong form, we should output instructions for correct usage and return main with a non-zero integer, typically 1.

Also remember to cast variables as required, otherwise cases like integer division will give an unexpected output.

# Files
## Streams and Redirection
Keyboard -> Standard Input (stdin)
Standard Output (stdout) -> Screen
Standard Error (stderr) -> Screen

We can redirect these to streams of our choosing from our shell:

```<``` for input redirection

```>``` for output redirection

These are OS features, not C features. However, this doesn't allow you to use multiple files. That flexibility comes with C files.

## C Files
```FILE *fopen(const char *filename, const char *mode)``` is used to open files as a stream.

```filename``` is a string representing the file name, ```mode``` is a string that can have values ```r``` to open the file for reading, ```w``` to open for writing and ```a``` to open the file for appending. Mode ```w``` starts writing from the beginning of the file, emptying the file if it contains any data alread. Mode ```a``` starts writing from the end.

```fprintf()``` can be used to output to specialised file streams.

Remember to close a file after you close it too with ```int fclose(FILE *stream)```, which returns 0 for a success and a non-zero number if failed.

```char *fgets(char *s, int n, FILE *stream)``` can be used to get at most ```n``` characters from ```stream```, including the null terminator, and put it into ```s```. This function stops reading when it reaches the end of a line. A failure to read any characters causes ```fgets()``` to return ```NULL```. A successful call moves the 'cursor' to the next line in the stream. Can also be used to read from ```stdin``` and is often more useful than ```scanf()``` which stops reading at a space character, while ```fgets()``` stops at a newline.

```gets()``` also exists, but is unsafe like ```strcopy()``` and ```strcat()``` and should not be used in practice. ```gets()``` does not let you specify the number of characters to read so an overflow might occur.

```int fscanf(FILE *stream, const char *format, ...)``` is like ```scanf()``` except it can read from any filestream, not just ```stdin```. This can be useful if we already know the structure of the lines in a file. ```fscanf()``` returns the number of values found on its call and might occur when a badly formatted list is read, or when the file ends.