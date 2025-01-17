Scheme 9 from Empty Space
=========================
```
                                ____  ____  ____  ____  ____
Scheme 9 from Empty Space      / ___)/ __ \|  __)|  __)/ ___)
A Portable Scheme Interpreter  \___ \\__  /|  __)|  __)\___ \
By Nils M Holm, 2007-2018      (____/(___/ |_|   |____)(____/
```

S9fES is a mature, portable, and comprehensible public-domain
interpreter for R4RS Scheme offering

- bignum arithmetics
- decimal-based real number arithmetics
- support for low-level Unix programming
- cursor addressing with Curses
- basic networking procedures
- an integrated online help system
- loads of useful library functions

It is written in ANSI C (C89) and Scheme and runs in many
popular environments, including *BSD, Linux, Plan 9, and the
unmentionable horror (although the Plan 9 and Horror versions
will exclude the Unix, Curses, and Networking extensions).

The S9fES code strives to be simple and comprehensible. It is
particularly interesting to people who want to

- have a Scheme system that runs virtually everywhere
- write Unix programs in a high-level language
- try Scheme without having to jump through too many hoops
- study the implementation of Scheme

There is a book describing a previous S9 implementation
in detail. It is available in print and PDF format. See
http://t3x.org/s9book/

S9fES supports the following SRFI's: 

- SRFI-0:  feature-based conditional expansion (subset)
- SRFI-2:  AND-LET*                            (subset)
- SRFI-22: running Scheme scripts on Unix
- SRFI-23: error reporting mechanism
- SRFI-30: nested multi-line comments
- SRFI-43: vector library                      (subset)

Bakul Shah maintains a downstream repository with extended
Plan 9 support, like access to system functions and an FFI:
https://github.com/bakul/s9fes.  Please send any feedback
regarding the Plan 9 extension directly to him.


## QUICK START

You can run an S9 interpreter in its build directory without
installing it. Just type

    cc -o s9 s9.c s9core.c
    ./s9 -i - -d s9.image

and then `./s9`. You might have to add `-Dplan9` on Plan 9 systems.
The interpreter will support full R4RS Scheme, but none of the
extensions provided by S9 (Unix system calls, Curses, etc).

A quick test of the interpreter can be performed by running

    ./s9 util/test.scm

In order to bring up the online help system, run

    ./s9 -l contrib/help.scm -d s9.image

When running `./s9` for the next time, the following help commands
will be available:

    ,h  explain the online help system
    ,a  return a list of all help topics (long!)

On most systems of the Unix family (plus CygWin and MinGW),
you can compile and install S9fES by running `make install`.

See below for further instructions.


## COMPILING AND INSTALLING

### Unix

To compile S9fES, run `make` and then `make test` (this will run
the test suite to make sure that the interpreter works properly).
Running `make tests` will run even more tests.

You can install S9fES (including the goodies) on a Unix system
by typing `make install` as root, but before doing so, please
edit at least the PREFIX variable at the beginning of Makefile.
Be sure to re-compile S9fES (`make clean; make`) after changing
PREFIX, because it is used to set up some internal paths.

### Plan 9

To compile S9fES on Plan 9 from Bell Labs, just type `mk`.
The "test" and "tests" targets will run tests, just like on
Unix.

To install S9fES, run `mk inst`. It will copy the S9fES binary,
heap image, function library, and help files to `$s9dir` (which
defaults to `/lib/s9fes`). Edit mkfile if you prefer a different
location.

The install procedure will also copy the file `util/s9.rc` to
`/rc/bin/s9` and adjust the `$s9dir` variable in that file.

### Manual Installation

To install S9fES manually,

- Compile s9 with a proper image directory and library path (the
  ones hardwired in `s9.c` are probably not what you want). E.g.:
  ```
  cc -o s9 -DIMAGE_DIR="\"dir\"" -DLIBRARY_PATH="\"path\"" s9.c s9core.c
  ```
  where "dir" should be set to S9DIR, as described below, and a
  reasonable value for "path" would be
  ```
  .:~/s9fes:/usr/local/share/s9fes
  ```

  Security-sensitive people may consider (re)moving the dot.

- Copy the `s9` binary to a location where it can be executed
  (e.g. `/usr/local/bin`).

- Select a publicly readable directory to contain library files.
  (default: `/usr/local/share/s9fes`). This directory will be
  referred to as S9DIR in the following instructions. Note
  that this directory *must* be contained in LIBRARY_PATH,
  as defined above.

- Compile a heap image and copy it to S9DIR:
  ```
  s9 -i - -d s9.image
  ```

  This step requires the file `s9.scm` to be present in the
  current directory.

  The image file must be named `s9.image` or it will not be
  loaded by default. An image loads significantly faster than
  source code.

Optionally:

- Copy the content of the `lib` directory to S9DIR. This
  directory contains lots of useful Scheme functions.

- Copy the content of the `contrib` directory to S9DIR. These
  files contain additional Scheme functions contributed by other
  authors and/or imported from various sources.

- Create a subdirectory named `help` in S9DIR and copy the
  content of the `help` directory to S9DIR/help. These files
  are part of the interactive help system. Note that `help/`
  does contain subdirectories!

- Copy the nroff(1) source code of the manual page "s9.1" to
  section 1 of your online manual (e.g. `/usr/local/man/man1`).
  In case you are not running Unix, there is a pre-formatted
  copy in the file `s9.1.txt`.


## CONFIGURATION

To create an image file containing additional functionality,
add the desired options to the `config.scm` file, run
```
s9 -i - -l config.scm -d s9.image
```
and copy the resulting image to your IMAGE_DIR.

If you are keeping your image files in a location other than
S9DIR, you can create the S9FES_IMAGE_DIR environment variable
and make it point to your preferred location. In this case
`s9 -i foo.image` will load $S9FES_IMAGE_DIR/foo.image.

You may also create the S9FES_LIBRARY_PATH environment variable
and make it point to S9DIR as well as other directories that
contain Scheme programs. The variable may contain a single
directory or a colon-separated list of directories that will be
searched in sequence for library files and help pages (in `help`
subdirectories contained in the given paths).

S9FES_LIBRARY_PATH overrides LIBRARY_PATH, so all directories
listed in the latter should also be present in the former.


## GETTING STARTED

Typing `s9` will drop you into the read-eval-print loop of the
interpreter. You can run Scheme programs non-interactively by
typing `s9 program.scm` at your shell prompt.

If you installed the extension library functions in S9DIR,
they can be loaded by the LOAD-FROM-LIBRARY procedure or the
",l" (comma ell) meta command:
```
> ,l draw-tree
; loading from /usr/local/share/s9fes/draw-tree.scm
> (draw-tree '(a b c))
[o|o]---[o|o]---[o|/]
 |       |       |
 a       b       c
> _
```
Feel free to explore them.

Running `s9help topic` on the shell prompt will print the S9fES
online help page about the given topic.


## ACKNOWLEDGEMENTS

I would like to thank the following people and organisations for
patches, bug reports, suggestions, hardware, access to hardware,
etc:

Alexander Shendi, Arun Srinivasan, Bakul Shah,
Barak Pearlmutter, Blake McBride, Bsamograd (reddit),
David Person, Dig1 (reddit), Dirk Lutzebaeck,
Doug Currie, Mario Deilmann, Masaru KIMURA,
McKay Marston, Nullbuilt0 (reddit), Peter, Ray Lai,
Torsten Leibold, Victor M. Muriel, Yi Dai,
and the Super Dimension Fortress (SDF.ORG).


## CONTACT

Nils M Holm < n m h @ t 3 x . o r g >

