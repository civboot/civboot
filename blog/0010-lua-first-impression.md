# Lua: first impressions

In my [last](./0009-lua-thoughts.md) blog post I discussed why the programming
language Lua should in theory be a reasonable choice to target.

In this blog post I'll discuss my first impressions with the language.

> Find the code in the http://github.com/civboot/fngi repository (`./civ.lua`)

## Best Possible Shell

One of the main things I wanted in Civboot (and in my day-to-day life really)
is a better shell language.

The main point is that I want all data on my system to be treated by my shell
as structured data, and I want to be able to then query that structured data
easily. This includes lazy queries like 'find the string "MyClass" anywhere' and
more detailed queries like 'find the process groups taking more than 33MiB of
RAM'

Pretty much the only common structured-type languages I know of which could be
used as a shell are python, ruby, javascript and Lua. So many typical
shell langauges (tcl, bash) are string-based and are not structured.  Languages
like SQL are a dumpster fire of language design (I won't ever again imagine
trying to use an SQL shell... --shiver--). Obviously compiled languages won't
work.

In a few days I managed to learn Lua and hack up a pretty awesome interface to
data, which could be extended to stored data (aka a minimalist database).
In only a few days and less than 1000 lines of code I wrote wrote this awesome
lib which includes checking and pretty printing the fngi type system. Super
impresive.

```
require('gciv')
local Employee = struct('Employee', {
    {'first', Str}, {'last', Str},
    {'title', Str},
    {'salary', Num},
})
-- Pretend you loaded this from a file or something
local employees = List{
  Employee{first='Bob',  last='Dole',  title='peon', salary=10},
  Employee{first='Jane', last='Smith', title='peon', salary=11},
  Employee{first='Lord', last='Ozark', title='manager', salary=100},
  Employee{first='Baron', last='Hutz', title='manager', salary=200},
  Employee{first='God', last='Emperor', title='vp', salary=100000},
}
```

Let's say I wanted only managers and vp's making less than 1000.

```
local p = Picker(Employee, employees)
print('Underpaid Managers:',
  p.title:in_{'manager', 'vp'} -- in this Set
   .salary:lte(1000))       -- less than or equal to 1000
   .select{'first', 'last'}
   :display())

-- prints:
first | last
----- + -----
Lord  | Ozark
Baron | Hutz
```

> Note: the display part is not yet implemented.

Now imagine this being used for everything you care about: listing files,
processes, searching files and folders -- a ergonomic way to query and
show things wrapped inside a tiny library.

Your grep (text search) could have a column for the dir, a column for the file
name and a column for the match. The match itself could be structured and have
things like a few lines of surrounding text already embedded in it. Think of
the profusion of options you have to give current command line tools to
get the kind of functionality which could be so easily expressed with a bit
of structured data.

## Types and the Metatable

I've only used Lua for a few days. My immediate impression was "wow, this
language is so unsafe". However, it is incredibly easy not just to modify how
"types" work in Lua but to **understand how the modifications are being
implemented**. Like C, I feel like I have a good mental model of how Lua gets
its power.

Lua come with basically no type checking and is way too lax IMO when for when
it returns `nil`. It's super easy to make a typo and and have a value being nil
deep in your stack, a feature I always hated in python.

However, unlike almost any language, Lua basically doesn't have a type system.
Instead it gives you the _tools needed to build a type system_. That tool? The
metatable -- and metatables work almost _exactly_ like interfaces work in fngi.

In Lua, a `metatable` is a special pointer attached to the table (value) which
is where the Lua interpreter looks for its special methods like `==` or when
it cannot find an index. This means that you can put your methods in your
metatable and override `__index` appropriately, and that function will be used
when an index can't be found. So implement your methods on your metatable and
you are good to go.

Because Lua does not really have a type system, it gives me complete control to
create my own type system which maps to fngi's architecture. Lua's place as a
best-in-class embedded languages shines for a use-case like Civboot where I need
a language that gets out of my way and let's me craft how I want things to work.

Lua is pretty awesome.
