# We need better spoken words for Hex

This is a short (pun intended) blog post about the understandability of our
systems.

Computers are hard to understand, but it doesn't have to be that way. The
purpose of [Civboot.org](http://civboot.org) is to think about all the ways,
both large and small, that we can more easily understand our computers.

An early thing that comes up when you "get down to the bits" is having a better
understanding of hexadecimal. And something we can do to help ourselves there
is to have a better vocabulary when speaking (or thinking!) hexadecimal numbers.

## What are Hexidecimal numbers?

Most people are used to counting in base ten. This means they count zero through
nine, and then the second digit gets incremented to become "10". We represent
this as

```
0 1 2 3 4 5 6 7 8 9 10 11 ...
```

Hexadecimal are base 16. This means there are 6 more numbers after nine (A - F):

```
0 ...         ... 9 A B C D E F 10 11 ...
```

## What is the problem?

Try to say these two numbers out loud:

 - 118,917
 - E1A,4D8

The first you are probably used to:

> one hundred eighteen thousand, nine hundred seventeen

The second is probably a bit trickier. You could just say each digit:

> "ee one ahe four dee eight

Altough the second is shorter, the names "hundred eighteen thousand" contains
context that helps our brains comprehend the number. The second looses that
context. Also, the second _already_ has a problem: "four dee eight" sounds like
"forty eight" -- not the number we mean.

The problem becomes even more severe when you try to "teen" and "enty" the
numbers. The second number becomes:

> eh hundred aheteen thousand four hundred deety eight

"Eh hundred" sounds like "eight hundred", "aheteen" sounds like
"eighteen"... you get the idea.

## Solution?
We need better names for the numbers A - F. I think the requirements are simple:

- has to be a single sylable, like the other numbers
- must be difficult to misinterpret from another number
- should start with the letter it represents

I propose the following:

- A: Ak
- B: Bell
- C: Cell
- D: Dell
- E: Ep
- F: Fell

So you would say 4AE as "four hundred akty ep"

You would say F1F as "fell hundred fellteen"

So what is [9 times F](https://www.youtube.com/watch?v=_zTpwNR5Bf4)? It's... hex
eighty nine, I have no idea where Erlich Bachman came up with "fleventy five",
whatever that means (I sincerely hope it doesn't somehow mean 0x89). Even F
times F is only epty one (0xE1), far short of what I think fleventy five was
supposed to mean (0xF5).

