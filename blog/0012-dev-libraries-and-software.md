# Civboot Development Libraries and Software

The essential devleopment libraries and software that I will build for Civboot
in the comming months include:

* [ele](http://github.com/civboot/ele): an extensible text editor written in Lua
  * shele: shell extensions for ele for interactively running commands
* loam: a key/value indexed database on the filesystem.
* pch: a version control system based on
  sequential "patches"
  * gate: A frontend for pch

All software will be written in Lua. I'd like to use this blog to jot down
several of my thoughts on the architecture of the components (except ele, for
that see the link).

All software follows the principle of simplicity first: I'm trying to make
the code as easy to understand as possible while still fulfilling the
requirements.

All names (except Ele) are named after gardening terms.

* "Loam" is the fertile soil where plants can both access and store nutrients.
  Software can both access and store data in the "loam database".

* Pch ("patch") is both the common term used for modifying software and refers
  to a "vegetable patch" aka a garden.

* "Gate" is the front of the vegetable patch and is the frontend for Pch

## loam: key/value store
There is need of some way to store data that can be quickly accessed (aka
indexed) as well as mutated.

Requirements:

* Storage of arbitrary amounts of data in the form of structured records
* Fast retrieval of arbitrary records by index
* Dynamic addition and mutation of records

The simplest way I can think of is:

* Have the _actual data_ be stored in an append-only file of records and
  record-updates. This means data is never directly deleted, only a record added
  which deletes an index. Records are structured data stored sequentially (like
  a list).

* Have the index be a separate file. The index file is a simple hash-map of
  record keys, with offsets into the record file.

* Mutations are handled by:
  * Appending a record to the record file
  * Keeping an in-memory hashmap of the mutated indexes.
  * Eventually persist the in-memory hashmap into a new index file and delete
    the old one.

* Occaisionally a GC run can be done which re-writes the record+index files to
  remove deleted/modified records.

* The source of truth is always the record file: the index file can be deleted
  and re-built entirely from only the record file.

I don't expect that this will be more than ~1000 lines of code (Lua).

## pch: patch based version control
Pch is an ultra-simple file-based version control system based on patches.  It
is very similar to mercurial in that it uses index-based commits.

A patch is simply a text blob which specifies what lines changed and how:

```
# path to the file and starting line
@"path/to/file.txt" 100

# The patch itself
-  0, |this line was removed from the left   (line=100)
+   ,0|this line was added on the right      (line=100)
+   ,1|this line was also added on the right (line=101)
```

The change trunk is simply a file containing a list of changes (like file
patches) with associated commit messages and other metadata. A change can
be more than file patches though. It also includes:

* topic (feature-request, bug, task, chat) creation, discussion and edits. This
  is the built-in issue-tracker for pch. Metadata allows categorizing where
  these get displayed and allow for closing/finishing topics.

* authentication: adding public key authorization for new changes. Allows
  the frontend to decide whether to accept a push/etc.

All changes have an id (simply an index). Branching is simple: a new change
trunk is created who's first change is a reference to the original trunk index.
To merge you must have a clean rebase and append the branch to the main trunk.

I don't expect this to be more than ~1000 lines of code either (using civlib
library for some things like the diff algorithm).

## gate: pch frontend
Gate will start as an extension for Ele but web-gate will eventually be a
web-frontend and backend. It permits browsing change history and
viewing/editing topics (i.e.  updating bugs).

> Note: Web-gate will not probably never run on the Civboot software stack (it will be
> Linux-only) but acts as a "gateway" for transitioning from Civboot to
> non-Civboot software.

I'm not sure the size of the Ele extension here, I can imagine this being a few
thousand lines of code. The web-frontend should use a lua framework (since it's
not strictly civboot software anyway) so shouldn't take up _too_ much LoC (it's
also less important to me that it's minimal).

