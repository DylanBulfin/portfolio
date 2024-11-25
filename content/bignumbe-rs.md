+++
title = "bignumbe-rs"
template = "page.html"
date = 2024-10-21
[extra]
summary = "A Rust library enabling incredibly large numbers with minimal overhead"
+++
This is a Rust library that allows you to store and do math with incredibly large numbers,
in arbitrary bases, relatively efficiently.

[(GitHub Link)](https://github.com/DylanBulfin/bignumbe-rs)

[(API Documentation)](https://docs.rs/bignumbe-rs/latest/bignumbe_rs/)

[(crates.io page)](https://crates.io/crates/bignumbe-rs)

## Inspiration
While looking at making an idle/incremental game, I quickly realized how big of a
limitation u64 and f64 impose. If you have a building whose cost grows by a factor of 
`1.5` each time you buy one, by the time you have 110 of them you've overflowed a u64, and
by the time you have 2000 you'll have overflowed an f64. I wanted something that would
allow me to use numbers much, much higher than this but without the memory footprint of an
arbitrary-precision library.

## Approach
I've changed direction in major ways several times during this project but from the
beginning the core idea has been the same: Similar to the floating point specification, we
can store a specific number of bytes for a significand and have the rest dedicated to
representing the exponent. 

### Representation
With something like this it's very important that you, from the very beginning, ensure
that there is only one way to represent a specific number. If we leave the range of the
significand unspecified this is impossible; 1 * 2^100 = 2 * 2^99 = 4 * 2^98, etc. So again
we take a similar approach to the floating point spec, e.g. we limit the significant to
one order of magnitude. So, we say that the exponent can only be non-zero if the
significand is in the range [2^63, 2^64 - 1]. 

#### "Compact" Numbers
As hinted above we only care about the range of the significand if `exp != 0`. For numbers
less than the maximium significand value (for binary `u64::MAX = 2^64 - 1`), we store them
as-is.

### Arbitrary Bases (Part 1)
Originally it was binary-only since computers use binary numbers and they're easy to work 
with. I realized that I needed a way to display the number in Base-10 exponential notation 
if it was going to be useful in an idle game. After trying and failing to come up with a 
good way to convert from my representation without introducing a huge amount of ambiguity,
I realized the easiest way to accomplish this would be to support arbitrary bases. My
first implementation simply stored the base as an integer on the main struct.

A core problem I had to solve when extending this to arbitrary bases is that we still need
to find a way to restrict the significand's range to one order of magnitude. But we don't
necessarily know what those bounds are until we calculate them. But calculating this is
expensive, and one of the most well-known restrictions of Rust is that it really dislikes
global mutables. So to support operations on an arbitrary base that may not be known at
compile-time, we have two main options:
1. Store metadata about the ranges on the `BigNum` struct itself. This is fine but a bit
   cumbersome. 
   - It also means we need to calculate the ranges on creation, which would mean
     the user would have to be careful about not creating them often, and clashes with my 
     goal of making it usable as basically a u64 standin.
2. Use a safe global mutable to cache the calculation the first time a base is used,
   making future uses faster. This can take several forms but I decided to go with a
   simple one: keeping a global `LazyLock<Mutex<HashMap<u16, Data>>>`. This also enables
   caching a table of powers which can be very useful for some bases
   - This approach is safe but resulted in ***huge*** hits to performance. Unfortunately
     there just isn't a way I could find to create safe global mutables without some
     tradeoff that made me reconsider

I considered making an attribute-type procedural macro that would take an int literal and 
calculate the ranges, store them in constants, and add references to the constants where
applicable. I still may try this at some point but at the moment I'm very inexperienced
with proc macros. 

### Arbitrary Bases (Part 2)
I realized this time that I could make use of Rust's generics system to allow efficient
operations on arbitrary bases, with the tradeoff of requiring more boilerplate (which I 
provide a macro for). 

I identified several methods that would be needed in a `Base` trait to allow efficient
math operations (among them a function that computes the `nth` power of a base, a function
that computes the 'magnitude' of a number in a given base, etc). There are default methods
for almost all functionality, but many of them can be overridden more efficiently if
performance is a concern (e.g. reading from a table for the `pow` function instead of
manually computing it). 
   
This comes with several benefits:
- There is no need for any special logic in any of the implementations for bases we want
  to treat differently, e.g. binary and hex. These bases simply override the trait methods 
- There is no need for an explicit `if rhs.base != lhs.base` at the beginning of each
  method. `BigNumBase<Binary>` and `BigNumBase<Decimal>` are two different types entirely
- Since adding special handling for additional bases can be done without changing the base
  struct code at all, it's much easier to provide a declarative macro that can do it
  [`create_default_base`](https://docs.rs/bignumbe-rs/latest/bignumbe_rs/macro.create_default_base.html)

I also would like to provide a proc macro that generates a more complete base, with a
const table of powers 
