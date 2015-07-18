# FnixForth
(A minimalist ColorForth variant)

fnix.img is a bootable floppy image for x86.
If you have qemu then from a console text terminal you may be able to
execute qemu.sh to emulate.
If you can boot from a floppy drive then you may be able to write
this image to a 1.44Mb floppy and boot it.
The boot sector enters protected mode, copies the first two floppy
cylinders (36 blocks each 1024 bytes) to address 0 in RAM, and starts
reading the FnixForth that begins in block 2 (hence only blocks 0
and 1 contain pre-compiled machine code).
The FnixForth code includes a block editor, floppy driver, and a
GA144 boot stream compiler I am calling Fivth.
The example Fivth code in block 24 generates a GA144 boot stream that
would drive a 2 character 7 segment LED display using 16 bits of
node 009 to cycle through octal digits.

The parameter stack is maintained by the bp register
with ax holding the top parameter on the stack.

Magenta Words
-------------
text ( -- n ) \ Address holding address for next 'read'.
here ( -- n ) \ Address holding next compilation address.
base ( -- n ) \ Address holding radix for literals.
sign ( -- n ) \ Address of characters of last token 'read'.
fuss ( -- n ) \ Address holding address jumped upon unknown 'sign'.

Blue Words
----------
;      ( -- ) \ If last compiled instruction was a call, change
              \ to a jump, else compile ret instruction.

Red Words
------------
read   ( -- ) \ Read one token from 'text' into 'sign'
              \ (up to 24 characters).
,    ( n -- ) \ Move n 'here' and then advance 'here' by 4.
c,   ( n -- ) \ Move the low byte of n 'here'
              \ and then advance 'here' by 1.
lit  ( n -- ) \ Compile code that leaves n on the data stack.
?lit  ( --? ) \ Test whether last compilation was for a literal.
fnix   ( -- ) \ Warm boot

White words are ignored.
Magenta, Red, or Blue words are defined.
Yellow, Cyan, or Green words must have already been defined.
(Literal numbers are treated as if they had been defined Magenta
at the address given by their value).
Then, according to how the word was defined:

Color |Magenta|         Red|        Blue|
------+-------+------------+------------|
Yellow| stack |        call|         N/A|
Green |   lit |compile call|        call|
Cyan  |   lit |compile call|compile call|

If a Green or Cyan word is found to have been defined both Blue and Red,
then the Blue word will be used.
