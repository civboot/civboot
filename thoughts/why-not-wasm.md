# Why Not WASM?

I have previously made comments, here and elsewhere, that web assembly seems like
a decent language to bootstrap from. After implementing an interpreter for a
subset of an early-version of the fngi language (including i32 bit integers,
function calls with inputs and locals and many other features) I now think I
was wrong. Here are the major issues:

1. WASM function inputs are part of the locals. This means that for _each
   function call_ a naive interpreter must first pop values from the stack and
   populate the inputs. This is the opposite of "inline". Clearly a complicated
   enough compiler can reason about these things, but this kind of complicaiton is
   exactly the kind of thing that Civboot wants to avoid.

2. WASM's core type system is 32 bit integers. This was obvious from the
   beginning, but I now believe that being able to support 16bit environments is
   highly important to Civboot, not least of all because it is simpler and
   lighter-weight for the initial language.

3. WASM relies heavily on indexes. Each module, global, function and local has an
   index. Allocating a bunch of memory for indexes is a hassle and non-optimal.

4. WASM locals act more like "registers" than "values on the local stack".
   Specifically, there is no way to (1) store anything larger than an i64 in a
   local or (2) get a reference to a local. Instead you must do this "virtually"
   by having a global value which represents your stack pointer and
   increment/decrement it when calling functions. But wait... isn't that the
   kind of task such a complicated VM should already be doing? I have all this
   machinery to deal with locals but can't actually get a reference to my
   locals? Not great.

WASM is not bad, it is just not the right design to run inline for simple or
memory-constrained targets. It's intended target is an optimizing compiler
**not** an embedded system which executes it at runtime.

