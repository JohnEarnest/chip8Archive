Squad
=====
Squad is a [Forth](https://en.wikipedia.org/wiki/Forth_(programming_language)) interpreter that runs on the [SCHIP](https://en.wikipedia.org/wiki/CHIP-8) virtual machine. It extends the 8-bit operations of this host machine to 16 bit "cells", per tradition, which also allows pointers to be easily transported and manipulated on the stacks. The parameter and return stacks have room for 32 cells apiece, and at startup there is a bit over 1kb of free space available for user definitions.

While many niceties are missing from the built-in lexicon, due to space constraints, Squad is a fairly complete Forth environment, with a conventional outer and inner interpreter, immediate words, and a mutable return stack. Squad's functionality compares favorably to [Applejak](../applejak) and [Bulb](../bulb).

Dictionary entries consist of a pointer to the previous entry (or 0 for the root entry), a "type" cell (see `>type`) which distinguishes immediate words with its high byte and word classes with its low byte, the name of the word as a string (see `>name`), and the body of the word itself (see `>body`). Strings are represented with a custom encoding in which each character is a byte, and strings are null-terminated by the byte `0xFF`. Properly-formed characters should be multiples of 5, and represent indices into the alphabet `0123456789abcdefghijklmnopqrstuvwxyz+-<>=!@.,:;[] `. Note that there are no newline or similar control characters in this encoding!

Controls
--------
The ASWD keys and E are always interchangeable with the arrow keys and space.

The outer interpreter will prompt the user for a token at a time, and then either append it to the current definition or execute it, based on `mode`. Whenever text is drawn with an inverted background, it is a pending input. Pressing right-arrow or space will confirm a selection, while the up and down arrows cycle between available options.

First, the outer interpreter will ask the user to choose between several coarse categories- a number (`num`), or one of six categories of word, described in detail below. Numbers are always shown in 16-bit hexadecimal. The up and down arrows will increment or decrement the number, while 2 and X will increment or decrement the the number by 256 at a time, to ease entering pointers and large values. If the user is ever prompted to enter a previously unused word name (see `name`), the entry can be terminated by choosing a space character (which will confirm the name up to but not including the space), or by pressing space (which will confirm the name up to and including the present pending character). Pressing Q while entering a new name will back up one character.

At the top level of the outer interpreter, the user may also press `Z`, which is a shorthand for entering `.s`; it will print the current contents of the parameter stack on a new line without modifying it.

Simple Examples
---------------
Double a number and print it out:
```
3 dup + .
```

Bump allocator:
```
: allot here @ + here ! ;
```

Calculate and print some terms of the Fibonacci sequence imperatively:
```
: fib  0 1 loop dup . swap over + dup 100 > until ;
fib
```

Vocabulary
----------
​The following word lists include stack effects in parentheses. The names to the left of the `--` sign are the input arguments, bottom to top, and the names to the right are the results, if any, bottom to top.

IO
---
Input and output words. Type code 1.

- `emit ( char -- )` print a single character to the terminal. The high byte of `char` is ignored.
- `space ( -- )` print a space to the terminal.
- `cr ( -- )` advance terminal output to a new line.
- `erase ( -- )` un-print a single character from the current line of terminal output.
- `type ( str -- )` given a string (as described above), print it to the terminal.
- `untype ( str -- )` erase a string, by issuing `erase` for each character.
- `num ( -- n )` prompt the user to enter a 16-bit number.
- `name ( -- )` prompt the user to enter a string for a new word name, appending this string to `here`.
- `word ( type -- xt )` prompt the user to select the name of an existing word. If `type` is 0, any word. Otherwise, it should be the type code of one of these categories.
- `token ( -- x flag )` prompt the user for a token, as in the outer interpreter. If `flag` is 0, `x` is a number. Otherwise it's a dictionary entry ("xt").

Core
----
Fundamental forth primitives. Type code 2.

- `dup ( x -- x x )` copy the top of the parameter stack.
- `drop ( x -- )` discard the top of the parameter stack.
- `over ( x y -- x y x )` copy the second item on the parameter stack.
- `swap ( x y -- y x )` exchange the top two items on the parameter stack.
- `r> ( | x -- x | )` move an item from the parameter stack to the return stack.
- `>r ( x | -- | x )` move an item from the return stack to the parameter stack.
- `! ( x addr -- )` write a 16-bit value `x` to `addr`.
- `c! ( x addr -- )` write an 8-bit value (the low byte of `x`) to `addr`.
- `@ ( addr -- x )` read a 16-bit value from `addr`.
- `c@ ( addr -- x )` read an 8-bit value from `addr`.
- `not ( x -- flag )` logically invert the flag `x`.
- `> ( x y -- flag )` is `x` greater than `y`?
- `< ( x y -- flag )` is `x` less than `y`?
- `= ( x y -- flag )` is `x` equal to `y`?
- `xor ( x y -- z )` bitwise XOR of `x` and `y`.
- `and ( x y -- z )` bitwise AND of `x` and `y`.
- `or ( x y -- z )` bitwise OR of `x` and `y`.
- `- ( x y -- z )` difference of `x` and `y`.
- `+ ( x y -- z )` sum of `x` and `y`.

Mem
---
Words concerning working with memory and dictionary entries. Type code 3.

- `here ( -- addr )` a variable containing the address of the first available cell of memory.
- `head ( -- addr )` a variable containing the `xt` of the most recent dictionary entry.
- `mode ( -- addr )` a variable containing 0 during interpretation or 1 during compilation.

- `, ( x -- )` write a cell to `here`, incrementing `here` by 2.
- `,c ( x -- )` write a byte to `here`, incrementing `here` by 1.
- `,ret ( -- )` append an `exit` to the current definition. This word performs tail-call elimination if the `exit` follows a threaded code subroutine call.
- `,lit ( x -- )` append a literal (push a number) to the current definition.
- `,jump ( addr -- )` append an unconditional branch to threaded code at `addr` to the current definition.
- `,jump0 ( addr -- )` append a conditional branch (branch if top of parameter stack is 0) to threaded code at `addr` to the current definition.
- `,call ( xt -- )` append a subroutine call to the current definition. This word will do the right thing whether the provided xt is a "native" (built-in) or threaded code subroutine.
- `>type ( xt -- n )` given a dictionary entry, get its category. High byte is the immediate flag, low byte is the category type code.
- `>name ( xt -- str )` given a dictionary entry, get the address of its name (suitable for `type`).
- `>body ( xt -- addr )` given a dictionary entry, get the address where its body begins.
- `create ( -- )` prompt for a name, and create a new dictionary entry with that name.
- `exec ( xt -- )` given a dictionary entry, execute the word. interpretation counterpart to `,call`.

Flow
----
Immediate words, mostly for dealing with control flow and compilation state. Type code 4.

- `if` ... (`else`) ... `then` basic conditional control structure. `if` consumes a boolean flag.
- `loop` ... (`again` | `until` | `while`) basic infinite and conditional loops. `until` and `while` consume a boolean flag.
- `:imm` prompt for a new name, and then begin defining an immediate word. Counterpart to `:`.
- `:` prompt for a new name, and then begin defining an ordinary word.
- `;` terminate a word and stop compiling.
- `exit` early return from a word; do not stop compiling.
- `[` switch to interpreting. Use with `]` and `,lit` to pre-evaluate constant expressions, for example.
- `]` switch to compiling.

Env
---
Helper words for maintaining and inspecting the Forth environment. Type code 5.

- `free ( -- x )` how many bytes of memory are available?
- `forget ( -- )` prompt for a word name, and destroy that dictionary entry as well as all later definitions. This can really donk up your interpreter if you apply it to internal bits of Squad.
- `.s ( -- )` display the contents of the parameter stack without altering it.
- `words ( -- )` list all available words.

User
----
"User-defined" (non-primitive) words. Type code 6. Some definitions are included here for test/demo purposes:
```
:    1+     1 +    ; ( x -- y )
:imm quote  0 word ; ( -- xt )
```
