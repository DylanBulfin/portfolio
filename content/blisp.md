+++
title = "BLisp"
template = "page.html"
date = 2024-11-08
[extra]
summary = "B(asic)Lisp: A Custom Programming Language"
+++
This is a custom programming language (specifically a dialect of Lisp) I designed, as well 
as an interpreter I wrote for it. 

[(GitHub Link)](https://github.com/DylanBulfin/blisp)

[(YouTube Demo Video)]TODO

I designed it to be as simple as possible while still being usable (as compared to
languages like Brainf*ck). The README in the GitHub repository has much more detail but
I've added some notes about the language below as well:

## Example Program
Below is a program which prints fibonacci numbers in order, with a half-second delay in
between.

```
([
  (def n1 0u)
  (def n2 1u)
  (def tmp 0u)

  (write n1)
  (write n2)

  (while true [
    (set tmp n1)
    (set n1 n2)
    (set n2 (+ n2 tmp))

    (write n2)

    (sleep 500)
  ])
])
```

## Types
BLisp supports the following types:
* `int` is a signed 64-bit integer type
* `uint` is an unsigned 64-bit integer type
* `float` is a double-precision (64-bit) float type
* `bool` is a boolean type
* `char` is an ASCII character (byte)
* `list<T>` is a list of elements of type `T`
    * Importantly, all elements of a list have to be coerceable to a specific type
    * `string` values are treated internally as `list<char>`
* `unit` is an empty value, like `()` in Rust or Haskell

### Type Coercion
We want to avoid having to manually mark every `uint` constant with its type so for 
numeric types we support coercion. Any literal without a negative sign or a decimal point
can be coerced into `int, uint, float`, while any literal with a negative sign but no
decimal point can be coerced into `int, float`

## Functions
The following functions are supported (more details in the README)
### Math
* `+` or `add`
* `-` or `sub`
* `/` or `div`
* `*` or `mul`
### I/O
* `write`
* `read`
### Control Flow
* `if`
* `while`
### Boolean Algebra
* `eq`
* `ne`
* `le`
* `ge`
* `lt`
* `gt`
* `and`
* `or`
* `not`
### Lists
* `concat`
* `prepend`
* `take`
### Variables
* `def`
* `init`
* `set`
### Convenience
* `tostring`
* `eval`
* `return`
* `sleep`

