It Doesn't Taste Like APLs...
=============================
Applejak is a very, very small interpreter for a subset of the [K programming language](https://en.wikipedia.org/wiki/K_%28programming_language%29) which runs on the Super CHIP-8 virtual machine; one of the most widely-ported VMs in existence. It was written in Octo Assembly Language as part of [Octojam 7](http://octojam.com/).

To cram this program (and all of its working buffers) into 4kb of RAM, I had to cut many corners. Error-handling is rudimentary-to-nonexistent. There's no composition, projection, bracket-application, lambdas, or dictionary types. Data consists of numbers (unsigned bytes) and lists, which may contain at most 16 elements. The workspace has room for 64 "atoms"- numbers or lists- and intermediate values may be stored in one of 6 variables. Expect some bugs.

If you don't know anything about K or APL, please don't let this misshapen little stump of an interpreter color your opinions. Perhaps you could try one of [my other K interpreters](https://github.com/JohnEarnest/ok)?

Controls
--------
CHIP-8 does not have an ordinary keyboard interface; just a hex keypad. Thus, to enter an expression at Applejak's REPL, you use a menu-driven tree-builder. Cycle between choices with the up and down cursor keys (or W/S), and confirm a selection by pressing the right cursor key (or D). On mobile devices, swipe up, down, or right, respectively. Choices that are not yet "confirmed" will be drawn with an inverted background.

At the top level, you can choose between the following types of tree nodes:

| Symbol                       | Name                | Meaning                                                                                               |
| :----------------------------| :------------------ | :---------------------------------------------------------------------------------------------------- |
| ![num](font-chars/tok_0.png) | Number              | Add a single unsigned integer (0-255).                                                                |
| ![var](font-chars/tok_1.png) | Variable reference  | Choose one of the variables X,Y,Z,A,B,C and retrieve its value (if any).                              |
| ![set](font-chars/tok_2.png) | Variable assignment | Choose a variable name, and enter an expression after the colon (:)                                   |
| ![mon](font-chars/tok_3.png) | Monadic verb        | Choose a monad (see below) followed by an input expression.                                           |
| ![dya](font-chars/tok_5.png) | Dyadic verb         | Choose a left expression (in parentheses), a dyad (see below), and a right expression.                |
| ![adv](font-chars/tok_6.png) | Adverb              | Choose a left expression (in parentheses), a dyad, an adverb (see below), and a right expression.     |
| ![lst](font-chars/tok_7.png) | List                | Enter a series of expressions, separated by semicolons (;) and terminated by a close parenthesis ()). |

Monads
------

| Symbol                        | Name    | Meaning                                                                                                                      |
| :---------------------------- | :------ | :--------------------------------------------------------------------------------------------------------------------------- |
| ![\*](font-chars/char_18.png) | First   | Get the first element of a list. Identity for non-lists.                                                                     |
| ![! ](font-chars/char_19.png) | Iota    | Generate a list from 0 up to a number N.                                                                                     |
| ![\*](font-chars/char_20.png) | Where   | For each number in a list, generate that many copies of its index. For 1/0 lists, this produces a list of the indices of 1s. |
| ![\|](font-chars/char_21.png) | Reverse | Generate a new list with the items of an input list in reverse-order.                                                        |
| ![~ ](font-chars/char_22.png) | Not     | Logically invert any numeric arguments. Recursively descends into list arguments. (Right-atomic.)                            |
| ![, ](font-chars/char_23.png) | Enlist  | Make a length-1 list containing any item.                                                                                    |
| ![r ](font-chars/char_24.png) | Length  | Get the number of items in a list.                                                                                           |
| ![? ](font-chars/char_32.png) | Random  | Given a number N, generate a list of N random bytes.                                                                         |

Dyads
-----

| Symbol                        | Name   | Meaning                                                                                           |
| :---------------------------- | :----- | :------------------------------------------------------------------------------------------------ |
| ![+ ](font-chars/char_25.png) | Plus*  | Add two unsigned bytes.                                                                           |
| ![- ](font-chars/char_26.png) | Minus* | Subtract two unsigned bytes.                                                                      |
| ![\^](font-chars/char_27.png) | XOR*   | Bitwise Exclusive OR of two unsigned bytes.                                                       |
| ![& ](font-chars/char_20.png) | Min*   | Minimum of two unsigned bytes.                                                                    |
| ![\|](font-chars/char_21.png) | Max*   | Maximum of two unsigned bytes.                                                                    |
| ![< ](font-chars/char_28.png) | Less*  | 1 if X is less than Y; otherwise 0.                                                               |
| ![> ](font-chars/char_29.png) | More*  | 1 if X is more than Y; otherwise 0.                                                               |
| ![= ](font-chars/char_30.png) | Equal* | 1 if X equals Y; otherwise 0.                                                                     |
| ![, ](font-chars/char_23.png) | Join   | Combine two lists or numbers into one flat list.                                                  |
| ![r ](font-chars/char_24.png) | Take   | Generate a list of the first X items of list Y, repeating as necessary, or X copies of an atom Y. |
| ![_ ](font-chars/char_31.png) | Drop   | Generate a copy of Y after removing the first X items.                                            |
| ![@ ](font-chars/char_33.png) | At     | Index elements Y from list X recursively.                                                         |
| ![~ ](font-chars/char_22.png) | Match  | 1 if X and Y are recursively identical; otherwise 0.                                              |
| ![? ](font-chars/char_32.png) | Find   | Return the index at a list X which matches Y; otherwise return an index 1 past the end of X.      |

\* an "atomic" dyad which will recursively penetrate to elements of lists. Adding a number to a list will add the number to each element of a list, while adding a list to a list will zip together matching elements of each list.

Adverbs
-------
Adverbs are verbs which modify the behavior of other verbs. In this case, they provide ways of applying dyads to every element of a list:

| Symbol                        | Name | Meaning                                                                     |
| :---------------------------- | :--- | :-------------------------------------------------------------------------- |
| ![/ ](font-chars/char_36.png) | Over | Apply the verb between each element of a list, and return the final result. |
| ![\ ](font-chars/char_37.png) | Scan | Like "Over", but accumulate a list of intermediate results.                 |
| ![' ](font-chars/char_35.png) | Each | Apply the verb between corresponding items of the left and right lists.     |

Errors
------
When Applejak crashes, it will attempt to display an error message. If you see an error message which does not look like one of the following, it suggests memory corruption. Whoopsie-doodle.

| Symbol                       | Name           | Meaning                                                                   |
| :--------------------------- | :------------- | :------------------------------------------------------------------------ |
| ![ws ](font-chars/err_0.png) | Workspace Full | Your calculation occupied too many atoms.                                 |
| ![len](font-chars/err_2.png) | Length Error   | Tried to construct a list that is too large to fit in a workspace "slot". |
| ![typ](font-chars/err_3.png) | Type Error     | Tried to apply a verb or adverb to inappropriate datatypes.               |

This program has also been published on [itch.io](https://internet-janitor.itch.io/applejak).
