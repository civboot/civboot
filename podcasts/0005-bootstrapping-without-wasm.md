# Episode 5: Bootstrapping WITHOUT Web Assembly

I have previously made comments, here and elsewhere, that web assembly seems like
a decent language to bootstrap from. After implementing an interpreter for a
subset of the languag (including i32 bit integers, function calls with inputs and
locals and many other features) I now think I was wrong. Here are the majori issues:

1. wasm function inputs are part of the locals. This means that for
   _each function call_ a naive interpreter must first pop values from the
   stack and populate the inputs. This is the opposite of "inline". Clearly
   a complicated enough compiler can reason about these things, but this kind
   of complicaiton is exactly the kind of thing that Civboot wants to avoid.
2. wasm's core type system is I32. This was obvious from the beginning,
   but I now believe that being able to support 16bit environments is highly
   important to Civboot, not least of all because it is simpler and
   lighter-weight for the initial language.


I'll probably think of other reasons in the future and finish this post.

