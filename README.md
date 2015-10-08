# Tenth
A simple bare-metal forth for PDP-10

## Why?
Ever since I got to play with [Jörg Hoppe's awesome KI10 front panel](http://retrocmp.com/stories/the-pdp-10-ki10-console-panel)
at the [VCFB 2014](http://vcfb.de/2014/) I wanted to do something with the PDP-10.
To have something to show at the [VCFB 2015](http://vcfb.de/2015/) I wrote a simple assembler
and started to write some basic console IO functions.
I began to really like the instruction set and started porting my very primitive [PDP-11 forth](https://github.com/aap/ainliforth).
Since this time I used an assembler it was much faster to modify the code and I got the basics working in time for the VCFB.
The next few days after the VCFB I extended it a bit to make it presentable.
I used jonesforth as a guide or inspiration for some things but the main code was written independently.

## Notes on the assembler
This is a very simple assembler. Its syntax is neither that of MACRO10 nor that of MIDAS. It reads from stdin and outputs `a.rim`,
which can be loaded by simh with `load a.rim`.

## Notes on forth
This forth is case sensitive, use upper case.

The PDP-10 is word and not byte addressed so I had to think of a way to store strings.
Storing an ASCII character (7 bits) into 36 bits seemed wasteful so I used the PDP-10's byte instructions to pack 5 characters into one word.
This makes handling strings a bit awkwards and currently all code handling them is implemented in assembly.
I've not decided how to expose this in forth yet.

Only a subset of forth words are implemented as of now. Since the only input method is via terminal all definitions
must be compiled by hand. The plan is to write a paper tape driver and to load forth code from there.

Defined words:
EXIT INTERPRET QUIT ABORT CREATE : ; CONSTANT VARIABLE BRANCH 0BRANCH (LITERAL) , [ ] . .S
DROP SWAP DUP ?DUP OVER ROT -ROT >R R> R@ RSP@ 1+ 1- + - * /MOD / MOD = <> < <= > >= 0= 0<> 0< 0<= 0> 0>= AND OR XOR NOT
ABS NEGATE MIN MAX WORD ! +! @ HERE STATE BASE >IN IF THEN ELSE BEGIN AGAIN WHILE REPEAT DO UNLOOP LOOP +LOOP I J EMIT KEY CR
SPACE

There is no real error handling. When a word isn't found in the dictionary it panics and goes into an endless loop.

I did not implement any standard although I used FORTH-79 for reference often.

Lastly I hope my assembly is at least somewhat idiomatic. I've been programming PDP-10 assembly for only 1-2 weeks now
so my experience is limited and I have not read much other PDP-10 code yet. I'd like to hear your criticism.

## How to run in simh
Compile `as.c` and assemble `tenth.s` with `./as < tenth.s`.
Start `pdp10` from simh and `load a.rim` to load the paper tape into memory.
Start execution with `go`
