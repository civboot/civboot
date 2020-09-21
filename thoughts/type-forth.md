# Typed Forth
> **WARN:** This is in thought experiment stage only.

It should be possible to add _types_ and _typechecking_ to forth with
zero runtime cost and _very little_ compiler and memory cost, while also
(mostly) adhering to the standard.

What is this magic? It all works in my head, lets see if it works on paper.

**Dictionary**: words in the dictionary have a few things added to them:
  - link: this is normal, it is the link to the previous dict entry.
  - name: this is normal, it is a padded counted string.
  - typeinfo: 4bytes after name which contains:
    - input-size (byte): the number of input types.
    - output-size (byte): the number of output types.
    - interface-size (byte): the number of interfaces implemented.
    - word-type (3bits): 
      - 0b000: the word is an unchecked function (normal forth word).
      - 0b001: the word is a checked function (normal typeforth word).
      - 0b010: the word is an enum
      - 0b011: the word is an method for a struct. Which struct is determined
        by the final input type.
      - 0b100: the word is a virtual interface.
  - input-types: input-size cells containing xt of the input types. The type checker can
    then lookup their types when compiling the type-stack.
  - output-types: output-size cells containing xt of the output types.
  - interfaces: interface-size cells containing xt of the interfaces implemented.
  - xt: this is normal, cell with addr of instruction for interpreter to call at runtime.
  - info: 4bytes containing
    - local-size (1byte): amount to increment the return-type to store local
      variables.
    - start (1byte): the total number of cells to offset left to get
      the start of this entry.
    - data-size (2byte): the number of cells in the data/code
  - data/code: the data of the word.
  - vtable: the vtable used for dynamic lookup of methods. Contains:
    - `xt`: the xt of this vtable's type, used in `vt>xt` to convert from
      virtual to concrete types at runtime.
    - `hash`: cell containing xt for this vtable's perfect hashing function
    - `vxts`: the virtual xts indexed according to the perfect hashing function.

In the interpreter, before xt is jmp'd to, the return stack is increased by
local-size (directly in assembly). After xt is jmp'd to the return stack is
decremented by that amount. The function code pulls the appropriate values from
the stack.

When declaring a word you can specify the type inside of `{ ... }`, which
can also specify local variables. Note that `{ ... }` is (mostly)
compliant with the ans standard with one caveat. To be compliant, the output
types after `--` must be correct _and_ you must specify a type of `u` (or other
single-cell type) for all words, i.e.  `u:local1` instead of just `local1`,
since type-forth will _not_ consume a variable that has no name while ANS forth
_will_. In other words { local1 ... }` in TypeForth would be a value of type
`local1` which is kept on the stack a.k.a. not a local variable.  Items that
are specified as 

Local type checking can be disabled if the first type is `nocheck:`, allowing
faster compatibility with other forth code and writing optized code when
necessary. 

Types are specified in a few forms:
  - `: myFun { flag u:index &MyArray:arr | u:i -- d }` this is a fairly
    standard usage. This function accepts an unsigned cell (index), a MyArray
    _pointer_ (&) and a flag. It also has a local named value `i`.  Only the
    first two inputs and the local variable are named, they can be accessed by
    name within the function. The flag is left on the stack but is still part
    of the word signature. All referenes are represented as a single cell (i.e.
    ptr)
  - `: myVFun { u:index &{ Vec+Debug }:vec -- d }` this accepts the vref
    (virtual reference) `vec` which must contain the `Vec` and `Debug`
    interfaces in it's vtable. All vrefs are represented as `vt ptr`,
    with the vt being the address of the vtable.
  - `: swap { ??a#1 ??b#2 -- ??b ??a }`: the input and output are untyped. The
    names (i.e. `??a`) are where to associate the types in the output, the
    numbers in the input names (i.e. `#1`) are the size of the input types.
    2swap would be `{ ??a#2 ??b#2 -- ??b ??a }`
  - `: d { ??a#2 -- d }`: the input is _untyped_ but is of a specific size. This
    is primarily for core types like `d`.
  - `: main { clear -- }`: `clear` means the function expects an empty stack.
  - `: clear { * -- }`: '*' is reserved for consuming the ENTIRE stack. Words of
    this type can only be used at the highest level or in functions that expect
    an empty stack (i.e. `{ clear -- ... }` or functions that clear the stack.
  - `: map1 { '{ ??a#1 -- ??a }:xt addr u }`: This word accepts the xt of
    another word, which it may then execute with a single cell. Likely (because
    of the name) a map1 will execute the function on `count` words at `addr`
  - `: myOptimizedFunction { nocheck: u -- u }`: the function is not
    type-checked, allowing for doing crazy things in LOOP/IF and using R> etc.
    You cannot use local variables in nocheck functions.
- When a word is compiled into the dictionary it encounters the `{ ... }`
  signature. It encodes the type indexes found into the `types` while incrementing
  the appropriate size. The type indexes are pushed into a compiletime only
  type-stack in the TYPEBUFFER
- While compiling a word, it keeps track of the type-stack by comparing and
  popping the xts of the input and pushing the xts of the output. It ensures
  that the values are kept consistent throughout.
- IF+ELSE+THEN or SWITCH statements are regarded as "block" and are handled thusly:
  - BLOCKSTART pushes the BLOCKSTART xt onto the block stack followed by a snapshot (copy)
    of the current type-stack.
  - It then compiles the types as normal, using the BLOCKSTART as the new bottom
    of the type-stack.
    - Note: If it encounters another BLOCKSTART then that block is checked
      first (by creating a new clone of the current stack) and it's
      input/output is used directly.
  - When it comes accross a BLOCKALT or BLOCKEND (i.e. ELSE, CASE, THEN, etc)
    it walks it's own stack backwards (the "return" stack of the block)
    comparing with the previous typestack. From this it infers the input/output
    types and encodes them.
      - This is rather simple actually. It starts at the bottom of both stacks
        and compares types until they don't match. These are discarded. The
        remaining types on the previous-stack are "input" types for the block
        and the remaining types on the block-stack are "output" types.
  - BLOCKALT (and any remaining BLOCKALT) do the _exact same procedure_. They
    clone the input stack etc. The difference is that when their types are
    inferred they compare them to the BLOCKSTART type signature.
- Most loops are not allowed to change the type-stack. BEGIN...AGAIN statements
  can return a value only within an internal block directly before a BREAK.
  - TODO: not sure how this is implemented, probably similar to above.

**Local values:** Local values are kept on the return stack, which is incremented
by the rstack-size before the word is EXECUTEd and the return addr put on the
stack. Usage of R> etc is _not permitted_ inside of type-checked functions for
this reason.  If use of R> is necessary, declare your function `nocheck`.

**Structs:** structs are have a word-type of struct. They have no runtime
behavior, and at compiletime simply assert the state of the type-stack. They
store their input types as thier fields and their output types as their own xt.
They also store the interface xts and construct their vtable (described more
below).

```
STRUCT
  field type1 name1
  field type1 name2
  implements Interface1
  implements Interface2
END
```

**Methods:** a concrete method can be added to any struct by simply defining a
:METHOD which accepts a ref to the struct as it's last input, conventionally
named `self` (or unnamed). The method can then be called with `.m myMethod`.
`m$` will lookup the method named "myMethod" using the last value on the
type-stack, which it will typecheck at compile time and execute at runtime.

**Enums:** enumerations are rust-like and can contain both a variant and a
value. They are very similar to structs but have a few default methods and some
extra data encoded after the vtable.
- enumInfo: cell containing further info about the enum.
  - `numvariants` byte encoded after the vTable contains the total number of
    variants (up to 255).
- variantTable: contains numVariants cells after enumInfo, each cell contains
  the xt of method associated with that vi (variant index).
- `: vi { &MyEnum:self -- u }`: method to return the vi (variant index) which is
  an index into the variantTable.

Each variant is then encoded with it's own method used in ECASE. For example:
- `:method uVariant { &MyEnum -- u } self @ ;`
- `:method dVariant { &MyEnum -- d } 1 d@i ;` double at index 1 of address
- `:method emptyVariant { &MyEnum:self -- } ;`


Example use below. SWITCHE conceputally calls `dup $m vi SWITCH`. Each ECASE is
the variant index followed by a lookup into the variantTable with an EXECUTE.
```
myEnumValue SWITCHE
  ECASE uVariant
    ... do stuff
  ENDCASE
  ECASE dVariant
     ... do stuff
  ENDCASE
  ECASE empty
    .... do stuff
  ENDCASE
THEN
```

Example definition
```
ENUM
  VARIANT empty
  VARIANT uVariant u
  VARIANT dVariant d
  IMPLEMENTS Debug
END
```

**VTable**: Space is reserved for the VTable when the Struct/Enum is
constructed and a perfect hashing function is dynamically created for the
struct to convert the xt of a virtual method into the appropriate method to
call. The xt of the perfect hashing function is stored in the first index
of the vTable.

To then assign methods to the type in question, use

```
IMPLEMENT Debug MyType
  dbg myDbg
END
```

