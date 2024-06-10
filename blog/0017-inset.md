# The inset operation

There is a missing operation from list libraries in every language I've seen,
which I have taken to calling `inset`. An inset is like an insert but is for
a range of values. An alternative (more wordy) name might be `replaceRange`.

First what most languages DO have for lists:

* `remove(self, index)`: this removes an item in the list. It is typically very slow
  (`O(n)`) since it requires shifting all items to the right of the index.
* `insert(self, index, value)`: this adds an item to the list at index. It has
  the same performance issue as `remove`.
* `set(self, index, value)`: this is your typical `list[index] = value`. It is a
  **remove** followed by an **insert** but is typically very fast (`O(1)`) since
  it is done in-place.

The new operation I'm proposing has the API `inset(self, index, values, rmlen=0)`.
This is the equivalent to removing index `[index:index+rmlen)` and then
inserting the `values` at `index`. In the case where `rmlen == len(values)` this
is also very fast (`O(values)`). In other cases this is at worst `O(n +
values)`. Let's look at how the inset operation works.

Let's say I had a list I wanted to insert into another, while also removing two
values:

```
# Exapmle python
[3, 4, 5, 6, 7].inset([8 9 0 1 2], rmlen=2)

# Translates to:
3 4 5 6 7    : original list
 |- -        : remove 4 5
 8 9 0       : inset after 3
||
\/
3 8 9 0 6 7 : final list
```

This could be broken down into the following steps
```
step 1: 3 4 5 6 7   : original list
step 2: 3 x x x 6 7 : move [6 7] to right, leaving space for new list
          ^ ^ ^
step 3:   8 9 0     : perform inserts into spaces
step 4: 3 8 9 0 6 7 : done
```

> Note: the 'x' values can be anything, all that matters is the items we were
> keeping are moved to the right

In the case where `len(values) == rmlen` we don't need to perform the move (or
rather, the move is already done). There is one more similar case: when `i +
rmlen >= len(self)` we don't have to perform any moves either since we are
replacing all values at the end (and more).

## Why
I'm writing a [text editor][ele]. In the text editor I treat the file as a list of
strings (list of lines). However, the data structure that actually implements
this list of lines can be either:

* a gap buffer
* a line indexed file
* a combination of the above (gap buffer for edited spans)
* a simple list of lines (not performant, test only)

I want these different data structures to expose the normal list APIs like
get/set index -- but I wanted all the span/slice mutation algorithms to boil
down to a single reasonably performant API, so I came up with the `inset`
operation. I'm pretty proud of it!

[ele]: https://github.com/civboot/civlua/tree/main/cmd/ele
