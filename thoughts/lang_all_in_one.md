What if instead of a "programming language", "markdown lanugage" and
"configuration language" they were all the same thing?

I've been working on http://github.com/vitiral/fngi and reading a little
about the Gemini protocol and markup language. Gemini is painfully minimal,
but it's gotten me thinking a little about what an all-in-one markup
would look like. Let's take a look.

The first thing is I like the idea that all control syntax has to happen as the
first several characters. In fact, I think it should be only the first
character, and that character should be `\`. Some examples:

```
\h1 heading 1
\h2 heading 2

This is plain,
\i but this is italic.
\b now this is bold.

Newlines are created by empty lines, like markdown.
If you want multiple newlines, use \n like:
\n
\n
\n
```

The following is a code block. END can be anything the user wants
the "terminating" string to be, but it must be on it's own line.

```
\code END
roses are red
violets are blue
this is some code
which is now through
END
```

Convention for code terminator might be `---`

```
\code ---
here's some code.
---
```

If you need to "escape" the control, use `\\`

```
\\ \code this is not code, but rather regular text.

  Initial whitespace is ignored,
  so this has no extra whitespace before the
  first words. If you need extra whitespace
  before words, you can escape.

\\ this does have whitespace
\\     before the words
```

You can also name blocks with `#` which appear as a global variable in fngi.
If you'd like to name some plain text

```
\text #mytext ---
This is some text, which will be available to fngi as the
variable `mytext` the same way it would be rendered (as one line).
---
```

So far, we havn't written any code that would actually run, just "code blocks".
To write fngi code that runs you use `\fngi`

```
\fngi @f_start ---
// This is fngi code and would actually run. It would have access to the
// variables already defined.

struct Person [
  first: str,
  last: str,
  age: u16,
];
---
```

This is useful for data and tables. The following is a table containing some
data that ends up actually mapping to global variables usable in fngi (user1
and user2). The default format for a table uses delimiter `|`, `\|`
escapes the delimter.  The first line is the header and uses # for the type.

```
\table --- ty=Person
var     | first   | last     | age
user1   | "bob"   | "smith"  | 42
user2   | "jane"  | "smith"  | 36
---
```

The above also demonstrates how to pass arguments to a table. By specifying the
`ty` we are saying that each `var` is a struct that uses those types.

Because fngi works this way, you can seamlessly mix documentation, variables,
configuration and code. Recall that fngi "macros" run at compile time, so it's
possible to construct configuration using both data and code dynamically at
"compile" time. Tables can even run code or use variables by just putting code
between the `|`.

If you would like the "rest of the file" to be code, you can use \fngi with
no end delimiter, like so

```
\fngi @f_end
// The rest of the file would now be code.
```
