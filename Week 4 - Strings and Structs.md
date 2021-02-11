# Week 4
# Strings
## Initialising
Defined as a char array with a special null character ```\0``` to mark the end of the string. Garbage values could be printed without the null character.

Initialising a string causes all the values in the char array after the last char in the string to be set to ```\0```.

Although it is legal for the initialiser and declaration of the char array to have the same length, it is a source of bugs since then there would be no way of adding ```\0```. We also don't need to explicitly declare size but remember that the amount of memory allocated remains fixed.

Declaring ```char *test = "Hello"``` declares an immutable string literal.

```char test[15] = "hello"``` sets the null terminator to the remaining elements.

```char test = "hello"``` initialises an array of size 6.

We can use the format specifier ```%s``` to print strings.

## Length
```sizeof()``` would not give an accurate result as it returns the amount of memory allocated for the whole char array.

```size_t strlen(const char *s)``` returns the size of the string excluding the null terminator.

## Copying
```char *strcpy(char *s1, const char *s2)``` copies ```s2``` and overwrites ```s1```. NOT safe as it doesn't play well when ```sizeof(s1) < sizeof(s2)```.

```char *strncpy(char *s1, const char *s2, int n)``` is safer and copies the first n characters of ```s2``` Make sure to account for the null terminator as ```strncopy()``` is not guaranteed to copy it.

## Concatenation
Adding two string variables is pointer addition, not concatenation.

```char *strcat(char *s1, const char *s2)``` appends ```s2``` onto ```s1```, starting from the first null terminator. Similarly unsafe like ```strcpy()```. Use ```char *strncat(char *s1, const char *s2, n)```. Make sure to account for the null terminator.

## Searching
```char *strchr(const char *s, int c)``` to search for the first occurence of a character and ```char *strstr(const char *s1, const char *s2)``` for the first occurence of a string.

# Structs
Defined by ```struct struct_name { //members };```

Members accessed via dot notation.

```struct_name->member``` equivalent to ```(*p).member```, where ```p``` is a pointer to ```struct_name```.

When structs are passed into arrays, they are copied into the stack frame of the function, so local changes are not reflected in the caller function; members including arrays are also copied. We could return the new struct but that is inefficient, so we pass in pointers to structs.

```typedef struct struct_name { //members } StructName;``` allows you to 'alias' ```StructName``` for ```struct struct_name```.
