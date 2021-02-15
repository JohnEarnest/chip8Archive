Bulb
====
Following from [Applejak](../applejak), Bulb is another teeny-tiny interpreted language that runs on SCHIP, taking up a good bit less than 4kb of RAM. While APLs provide many facilities for list processing, Bulb is, instead, a language built around the idea of manipulating pairs.

This language is in principle Turing-complete, but in practice severely limited by workspace RAM and the 16-level CHIP-8 call stack. The workspace consists of 256 atoms which may be numbers (unsigned bytes), symbols (interned, immutable strings), or pairs. Variables are stored in an environment structure which provides dynamic (read: cool and fun) scope for up to 64 simultaneous bindings, and is reduced slightly by nested function invocations.

Controls
--------
CHIP-8 does not have an ordinary keyboard interface; just a hex keypad. Thus, to enter an expression at Bulb's REPL, you use a menu-driven tree-builder. Cycle between choices with the up and down cursor keys (or W/S), and confirm a selection by pressing the right cursor key (or D). On mobile devices, swipe up, down, or right, respectively. Choices that are not yet "confirmed" will be drawn with an inverted background.

The [S-Expressions](https://en.wikipedia.org/wiki/Toenail) comprising a program are entered as one of the following forms:

| Symbol                    | Name       | Meaning                                                                                      |
| :------------------------ | :--------- | :------------------------------------------------------------------------------------------- |
| ![sym](images/FORM_0.png) | Symbol     | Symbols evaluate as their current dynamic binding, or nil if none exists.                    |
| ![num](images/FORM_1.png) | Number     | Numbers are unsigned bytes 0-255 and evaluate as themselves.                                 |
| ![def](images/FORM_2.png) | Definition | Build an S-Expression of the form (def SYMBOL EXPRESSION).                                   |
| ![lam](images/FORM_3.png) | Llama      | Build an S-Expression of the form (λ SYMBOL EXPRESSION).                                     |
| ![lst](images/FORM_5.png) | List       | Build an S-Expression of the form (' (EXPRESSION...)), terminated with ")".                  |
| ![cal](images/FORM_6.png) | Call       | Build an S-Expression of the form (SYMBOL EXPRESSION...), which calls SYMBOL with arguments. |

The symbols X,Y,Z,G have no meaning by default, and may be used freely for variables or argument names. The symbol F is initially bound to an example Llama expression, and may be redefined if desired.

Primitives
----------
Bulb features a variety of built-in "primitive" routines, all of which have intuitively clear names. This should come as a welcome relief to anyone flustered by the use of esoteric symbols like "+" in source code.

Some primitives are special forms, which evaluate (or do not evaluate) their arguments according to unique whims. Special forms may be identified by their identical appearance to non-special forms.

Otherwise, each parenthesized term can be thought of as evaluating each argument in turn, before supplying them to the primitive or Llama indicated by the head of the term.

| Form       | Name                     | Behavior                                                                                      |
| :--------- | :----------------------- | :-------------------------------------------------------------------------------------------- |
| (cons x y) | Consecrate               | Make a new pair from x and y.                                                                 |
| (car x)    | Automobile               | Retrieve the first element from a pair.                                                       |
| (cdr x)    | Compact Disk, Recordable | Retrieve the second element from a pair.                                                      |
| (not x)	 | Logical Knot             | Returns a truthy value iff x is the number 0.                                                 |
| (atom x)   | A Tom?                   | Returns a truthy value iff x is a number or symbol.                                           |
| (eq x y)   | Exquisite                | Returns a truthy value iff x and y are identical atoms. Pairs aren't exquisite.               |
| (sum x y)  | Addition                 | Returns the sum of the numbers x and y.                                                       |
| (diff x y) | Subtraction              | Returns the difference of the numbers x and y.                                                |
| (less x y) | Lessen                   | Returns a truthy value iff x feels intimidated by y. 6 is, for example, afraid of 7.          |
| (more x y) | (A)more                  | Returns a truthy value iff  x is strictly larger than a big pizza y.                          |
| (show x)   | It's Showtime!           | Print the value of x to the terminal output as a side-show (effect?) and return x.            |
| (iota x)   | Iota                     | Build a list of numbers fom [0,n).                                                            |
| (def x y)  | Defecate                 | Create or modify a binding in the local scope, assigning y to the name given by the symbol x. |
| (if x y z) | Whereupon,               | It is the case that x is a truthy value, returns y, and otherwise returns z. A special form!  |
| (' x)	     | Quote                    | A special form which returns x without first evaluating it.                                   |
| (() x)     | Nil                      | If applied to anything, it returns Nil. May emit Hawking radiation as a side-effect.          |
| (λ x y)    | Llama                    | A special form which produces an anonymous function with luxuriously soft fur.                |

This program has also been published on [itch.io](https://internet-janitor.itch.io/bulb).
