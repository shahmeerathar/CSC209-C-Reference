# Week 11
# Shell Programming
## Shell and scripting
Basically a loop: prompt -> read -> parse -> execute -> prompt
Two main flavours of shell programming languages:
- sh -> Bourne, ksh, ash, bash, dash (always tested under at least two different versions to ensure we're not using any features unique to the scripting language)
- csh -> csc, tcsh. Inferior to sh.

``*`` is used as a wildcard for substitutions.

``sh scriptname`` to execute a script.

In sh, everything is a string.

Variables defined as ``varname=val``. Note that there are no spaces, otherwise it would be interpreted as a command with arguments. ``$varname`` to substitute variable name with its value.

The value of the variable is evaluated as a string, kind of like the macro language. We can use the utility program ``expr`` to do arithmetic.

``echo`` is effectively used as a print statement.

Backquotes (`) are used as another substitution wildcard to execute a command, encapsulate and substitute its output. NOT the same as single quotes.

The ``read`` command reads input into a variable. Can be used to read multiple tokens into multiple variables separated by whitespaces. If there are more tokens than variables, all the last n tokens go into the last variable. If there are more variables than tokens, then subsequent variables are assigned the empty string.

Common for shell scripts to invoke C programs. The shell looks through the ``PATH`` directory to find these programs. The shell looks through the colon separated directories in `PATH` until it finds the requested executable (starting without a ./).

## Control constructs in shell programming
Based on exit statuses as boolean values; commands in Unix succeed or fail.

`$?` gives us the exit status of the last command. 0 = success, else fail.

Conditionals:
```
if condition
then
    command
elif command
then
    command
else
    command
fi
```

Remember that `then` has to be on another line since `if` executes an arbitrary command with an arbitrary number of arguments.

``test`` is used to compare values, strings files, booleands and is also aliased as `[ expression ]`.

While loop:
```
while condition
do
    command
done
```

``true, false`` are used as boolean values: true = `exit(0)`, false = `exit(1)`

``/dev/null`` is a simple device driver (returns 0) to dump data we don't need.

Use `&&` and `||` as logical operators.

`:` is used as a null statement in sh, similar to pass in Python.

## Quotes and further control constructs
`\` to suppress the special meaning of a single following character.
`''` and `""` for a whole sequence.

`""` suppress everything except dollar sign, backquote, backslash and the closing double quote. `''` suppress everything except the closing single quote. A space is also a special character whose meaning is suppressed by quoting.

For loop (Pythonic):
```
for i in strings
do
    command
done
```

Loops over all the strings given as arguments.

``for varname`` loops over all the command line arguments.

`seq` is similar to `range` in Python.

Case:
```
case $i in
    case1)
        command
        ;;
    case2)
        command
        ;;
    *)
        default
        ;;
esac
```

`*` matches anything but only if case1 and case2 don't match. Can also be used as part of the non-default cases.