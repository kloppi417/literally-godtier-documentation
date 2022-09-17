# Introduction

High-Level Brainfuck intends to minimize boilerplate (but uses semicolons because I'm too lazy to write a JavaScript-style ASI), so an empty file is valid code and can be compiled and executed. HLBF files use the `.hlb` or `.hlbf` file extension. HBLF uses a C-like syntax, with curly braces (`{}`) to indicate scope, and statement declarations terminated with a mandatory semicolon (`;`). Parentheses (`()`) are used for grouping. 

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

## Functions

Functions are declared with the `function` keyword, followed by the name of the function and two parentheses. Functions have static return types, indicated by an `->` operator and the name of a datatype after the parentheses. To return nothing, omit the datatype, but keep the arrow operator. To declare parameters, put the datatype and name of the parameter inside the parentheses, and separate each parameter with a comma. Finally, write an opening and closing curly brace. Any code written inside of the curly braces will be executed when the function is called.

### Scoping

By default, all variables declared outside of a function are global. Global variables can be accessed inside of a function. Variables declared inside of a function are local, and cannot be accessed outside of that function. The memory that local variables use is freed up when the execution of the function ends.

### Examples
```elixir
import stdio;
function fc() -> {
    print("Hello world!");
}
fc();
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
```
## Packages

Packages are a collection of user-written functions that can be imported and used in your code. Package source-code files are written in HLBF, and must have the `@package` decorator at the top of the file. To use a package, create a `packages` folder in the same directory as your main HLBF file, and put the package source-code into that folder.

To use a package, in your main file write `import` followed by the name of the package file, with the file extension omitted. This imports all the functions declared in the package file. Variables can be used in writing packages, but cannot be imported. To import multiple packages on one line, write the names of all the packages you want to import, separated by commas.

By default, there are two packages globally installed: `stdio` and `math`. These two packages can be used by other packages, but non-globally installed packages cannot be used. As of now, extra packages cannot be globally installed.

### Examples

*Excerpt from the [math](https://www.google.com) package:*
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
```

**Note about the stdio package**: as of now, the stdio package cheats in how it works. HLBF variables actually exist in Brainfuck memory, rather than solely existing in the compiler's memory, and everything to do with variables, such as copying one variable to another, uses this memory actually stored in Brainfuck. However, when printing a variable with stdio, the compiler pre-computes the value that needs to be outputted, rather than using the Brainfuck variable memory.