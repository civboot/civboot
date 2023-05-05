*Download on
[anchor](https://podcasters.spotify.com/pod/dashboard/episode/e23iq6a)
or wherever you get your podcasts, or watch on
[youtube](https://www.youtube.com/watch?v=5Kq1zlYtXwg).*

# Fngi Intro

Announcement: I have finally started to write [fngi] code!

[fngi]: http://github.com/civboot/fngi

Fngi is my personal attempt at creating a minimalist _but understandable_
programming language for [Civboot] that is generally useable even on minimal
hardware, such as microcontrollers with no virtual memory management.

In this post I want to give a preview of what fngi is and some of the major
architecture decisions that went into it's creation. An accompying podcast will
also be released shortly.

Each section will be flushed out with their own post/podcast in the future.

[Civboot]: http://civboot.org

## Introduction

Fngi is an imperative, strongly-typed, compiled, low level language. It has:
* A syntax and requirements similar to C
* An implementation architecture similar to Forth (tiny)
* A type system which draws from the simplest and most useful ideas of
  Rust.

Fngi is a macro-first language: it gives maximum power to the programmer to
extend the syntax or access the compiler internals (such as the type system).

There is a lot to unpack there and we will do so in the following sections.

### Tiny Implementation
The fngi compiler today is only about 3000 lines (written in C), and is not
likely to get much larger. This is inline with many FORTH's, which as you might
know are some of the [smallest complete languages in
existence][collapseos-forth]

[collapseos-forth]: http://collapseos.org/forth.html

### Syntax
Fngi's syntax departs from C on several mundane points (such as braces vs
parenthesis) but should be very familiar to most C programmers. I have
personally used many types of languages (C, Python, Java, FORTH, Elm) and C
"gets the job done" on expressibility for many things. Where C fails is it's
type system and macro system.

Calculating the Nth fibonacci number in C and fngi:

```c
// C Syntax
uint32_t fib(uint32_t n) {
    if(n <= 1) return 1;
    return fib(n-1) + fib(n-2);
}
```

```fngi
\ Fngi Syntax
fn fib n:U4 -> U4 do (
  if(n <= 1) do ( ret 1 )
  fib(n-1) + fib(n-2)
)
```

### Types
Fngi has types, including pointers. Typically types are checked at compile time,
although the programmer can disable that (at the loss of some functionality) or
directly alter the type system using syn functions (aka macros) -- we'll get to
that.

Types include structs and enums. Structs are exactly like C structs: they let
you package a set of named types such as integers or other structs into your own
type. Enums are like Rust enums: they are a value which contains exactly one of
some set of types.

```
struct MyStruct [ a: U4;  b: U2;  c: &MyStruct ]

enum MyEnum [
  unsigned: U4;
  signed:   I4;
  string:   String;
  myStruct: &MyStruct;
]
```

### Data Ineritance (a.k.a. single inheritance light)
Fngi also has single inheritance.  Like many things in fngi, inheritance is
similar to but drastically more constrained than other languages like C++ or
Java. In fngi it is absurdly simple: if `Child` is a child of `Parent`, then
`Child` simply has the exact same fields as `Parent` at its beginning -- plus it
can have its own fields after the parent.  This means you can safely convert a
`&Child` (pointer to child) to a `&Parent` (pointer to parent) with zero
arithmetic. It also means you can call any of `Parent`'s methods, which afterall
just take a `self: &Parent` as the first argument.

```
\ A "slice" or view of some U1 data of length len
struct Str [ dat: &U1;  len: U2 ]

\ A child of Str with additional available capacity (cap)
\ StrBuf can:
\ - call any methods defined on Str.
\ - an &StrBuf will be auto-cast to &Str (but not vice-versa).
struct StrBuf [ parent: Str;  cap: U2 ]
```

### Roles (a.k.a. interfaces light)

Fngi also has something very similar to runtime [interfaces]: it calls them
"roles" (a.k.a. a struct or enum can "play a role"). A role value is just a
struct containing two fields: a pointer to methods + a pointer to data. The
methods must take the data pointer as their first value. The compiler knows how
to convert `myRoleLocal.myMethod(1, 2)` into the correct pointers.

[interfaces]: https://en.wikipedia.org/wiki/Interface_(object-oriented_programming)

```
\ A resource role with a single method
role Resource [
  \ You can always drop a resource
  meth drop(&Self) do;
]

\ An example UnixFile struct.
struct UnixFile [ U4 fd;  arena: &Arena;  ... ]

\ An example method in UnixFile
with:UnixFile [
  meth drop(self:&UnixFile) do (
    unix.close(self.fd)
    \ Calls self.arena.free with size+alignment of the UnixFile type.
    autofree(self.arena, self)
  )

  ... other methods
]

implement Resource for UnixFile {
  \ Explicitly specified for demonstration, not required
  drop=&UnixFile.drop
}
```

### Syn Functions (a.k.a. macros heavy)
Perhaps fngi's most extreme feature is it's syn functions which create an ultra
powerful (and dangerous!) macro system which can:

* Call any fngi function and read/modify defined global variables.
* Control the parser, including parsing tokens or custom syntax.
* Write bytecode directly to the current "code" location. Bytecode is a virtual
  machine language called "spor".
* Control the "type database" including pushing and popping types, allowing for
  macros to generate different code depending on types or syntax.

This goes way beyond the text-based macros of languages like C (although Rust
has [something similar][rust-procedural]). You can implement things like `while`
loops, `switch/case` statements or even define your own way to create c-like
text macros. Almost any feature you think fngi is missing can be implemented in a
library, with nearly full access to any compiler or type intrinsics.

[rust-procedural]: https://doc.rust-lang.org/reference/procedural-macros.html

### Low Level
Fngi brings low level programming to a whole new level!

Like C, fngi requires manual memory management. Unlike C, fngi's standard
library supports custom allocators. Also unlike C, fngi's standard library
requires only a maximum contiguous memory of 4KiB blocks -- something which can
be trivially implemented on a microcontroller.

In general, fngi is designed so that even it's "dynamic" libraries can work well
on very minimalist hardware, meaning it is easy to build software which does
dynamic things (like write and compile programs) on minimalist hardware.

Fngi's use of roles makes the abstraction of the "operating system" and a
program simpler and easier to test.

