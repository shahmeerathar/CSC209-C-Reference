# Week 10
# Bit Manipulation

## Logical and Bitwise Operators
0 = False and 1 = True

&& = logical AND, & = bitwise AND

|| = logical OR, | = bitwise OR

^ = bitwise XOR, 1 only if exactly one operand is 1

~ = bitwise negation

Be mindful of the difference between logical and bitwise operations. For lofical operators, a value is considered to be false if it is zero, and true if it isn't.

C doesn't define non decimal constants, but GCC allows you to do it by prefacing `0b` for binary and `0x` for hex. Hex is convenient since 1 hex digit is 4 bits.

## Shift Operators
Used when you want to construct a binary number with a specific value at an arbitrary bit position.
``b << a`` to shift `b` by `a` number of bits left. ``>>`` for right shift.

Note that numbering starts from the right at position 0.

## Flag Bits and Bit Vectors
Used by system calls when a single value is used to transmit data about multiple options.

An octal digit in C is written with a preceding 0.

Use bit operators to 'mask' specific bits to store data efficiently.

# Multiplexing I/O
``read()`` on a pipe blocks unless there's something in the pipe, or the pipe is closed.