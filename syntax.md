# Syntax

### Table of contents:

1. [Introduction](#introduction)
2. [Variables](#variables)
3. [If-statements and Conditionals](#if-statements-and-conditionals)
4. [Loops](#loops)
5. [Functions](#functions)
6. [Packages](#packages)

## Introduction

High-Level Brainfuck (HBLF) uses a C-like syntax, with curly braces (`{}`) to indicate scope, and statement declarations terminated with a mandatory semicolon (`;`). Parentheses (`()`) are used for grouping. HLBF intends to minimize boilerplate (but uses semicolons because I'm too lazy to write a JavaScript-style ASI), so an empty file is valid code and can be compiled and executed. HLBF files use the `.hlb` or `.hlbf` file extension. 

If you learn languages by examples, example HLBF code is available in this document, and example files can be found [here]().

## Variables

Variables are declared with a primitive datatype keyword, followed by the variable name. Assignment is the variable name, followed by an equal sign (`=`) and a value. Variables are statically typed, meaning the value of the variable must correspond to the datatype of the variable. The datatypes currently in HLBF are:

* `int`: 32-bit signed integer
* `bool`: boolean
* `str`: variable-length string that can hold all valid ASCII characters

Variable initialization and assignment can be combined to one line, using the syntax `<datatype> <name> = <value>;`. Initialized yet unassigned variables are technically not empty, but hold garbage data from previous executions or variables that went out of scope. However, the compiler will stop you from accessing variables until they are assigned, to prevent bugs.

Variables are mutable by default. You can declare a immutable constant by including the `const` keyword before your variable intialization. Constants *must* be assigned on the same line as the initialization, or the compiler will throw an error.

### Examples
```elixir
int n;
n = 5;
m = n;
```
```elixir
bool x = false;
const bool y = true;
```
```elixir
str hi = "Hello world!";
hi = "Hello, doc reader!";
```

## If-statements and Conditionals

If-statements are initialized with the `if` keyword, followed by a set of parentheses. Inside the parentheses, and a boolean or conditional statement. The conditional operators currently in HLBF are:

* `==` - equality
* `>` - greater than
* `<` - less than
* `||` - or
* `&&` - and
* `!=` - not equal
* `>=` - greater than or equal to
* `<=` - less than or equal to

A conditional statement is any statement with a value, conditional operator, and another value. Values can be variables as well as complex values made up of arithmetic. Conditionals evalute to booleans, so a variable set to a conditional either holds `true` or `false`, not the actual conditional.

The not operator (`!`) reverses the value of any boolean it's put in front of, i.e. `!true` is `false` and `!false` is `true`. The not operator can be used in front of variables as well as conditionals, but the conditionals must be wrapped in a set of parentheses (ex: `!(1 > 5)` evaluates to `true`).

### Examples

```elixir
if (1 < 5) {
    print("Less than!");
}
# Prints 'Less than!'
```
```elixir
const int x = 5;

if (x + 1 > 5) {
    print("Math!");
}
# Prints 'Math!'
```
```elixir
bool x = !(5 > 6);
bool y = 99 < 0;

if (x && !y) {
    print("Not operator!");
}
# Prints 'Not operator!'
```

## Loops

Loops are declared with the `while` keyword, followed by a set of parentheses and a conditional, and then a set of curly braces. While the conditional evalutes to true, the code inside of the curly braces executes. **Loops are thread blocking**, meaning that the execution of the program will be halted until the loop finishes. Infinite loops, therefore, will crash the compiler.

There are no `for` loops in HLBF.

### Examples

```elixir
int i = 0;
while (i < 10) {
    print(i);
}
# Prints 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
```
```elixir
import stdio;

bool done = false
while (!done) {
    user = input();
    if (user == "exit") {
        done = true;
    }
}
```

## Functions

Functions are declared with the `function` keyword, followed by the name of the function and two parentheses. Functions have static return types, indicated by an `->` operator and the name of a datatype after the parentheses. To return nothing, omit the datatype, but keep the arrow operator. To declare parameters, put the datatype and name of the parameter inside the parentheses, and separate each parameter with a comma. Finally, write an opening and closing curly brace. Any code written inside of the curly braces will be executed when the function is called.

Functions are called by writing the name of the function, followed by a set of parentheses with the required arguments, separated by commas. Functions must be defined before they can be called.

### Scoping

By default, all variables declared outside of a function are global. Global variables can be accessed inside of a function. Variables declared inside of a function are local, and cannot be accessed outside of that function. The memory that local variables use is freed up when the execution of the function ends.

### Examples
```elixir
import stdio;

function main() -> {
    print("Hello world!");
}
main();
# Prints 'Hello world!'
```
```elixir
function add(int a, int b) -> int {
    return a + b;
}
three = add(1, 2);
```
```elixir
import stdio;

function greeting(str name) -> str {
    str greet = "Hello, ";
    return greet + name;
}
print(greeting("world"));
# Prints 'Hello, world'
```

### Built-in functions

HLBF provides a few built-in functions:

* `len(str s)` - returns the length of the string passed as the argument `s`.
* `bf()` - allows you to write pure Brainfuck code inside the paratheses, using up to 25 cells of Brainfuck memory. Trying to move the pointer past the 25th cell will result in the pointer wrapping around to the first cell.

## Packages

Packages are a collection of user-written functions that can be imported and used in your code. Package source-code files are written in HLBF, and must have the `@package` decorator at the top of the file. To use a package, create a `packages` folder in the same directory as your main HLBF file, and put the package source-code into that folder.

To use a package, in your main file write `import` followed by the name of the package file, with the file extension omitted. This imports all the functions declared in the package file. Variables can be used in writing packages, but cannot be imported. To import multiple packages on one line, write the names of all the packages you want to import, separated by commas.

By default, there are two packages globally installed: `stdio` and `math`. These two packages can be used by other packages, but non-globally installed packages cannot be used. As of now, extra packages cannot be globally installed.

### Examples

*Excerpt from the [math]() package:*
```elixir
@package

function square(int n) -> int {
    return n * n;
}
```
*Usage in an HLBF project:*
```elixir
import math, stdio;

int x = 2;
print(square(x));
# Prints 4
```

**Note about the stdio package**: as of now, the stdio package cheats in how it works. HLBF variables actually exist in Brainfuck memory, rather than solely existing in the compiler's memory, and everything to do with variables, such as copying one variable to another, uses this memory actually stored in Brainfuck. However, when printing a variable with stdio, the compiler pre-computes the value that needs to be outputted, rather than using the Brainfuck variable memory.
