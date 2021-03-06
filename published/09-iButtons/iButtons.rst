:title: iButtons
:date: 2005-05-16 11:27
:slug: 9-iButtons

I was talking with Pavel (aka PenguinOfDoom, on #twisted) last week about
iButtons, and mentioned the JavaButton I picked up years ago that I haven't
really managed to do anything with yet. That prompted me to poke around the
web site (was dalsemi.com, since bought by http://www.maxim-ic.com), and it
turns out they have a new-ish version of the portable C code that interfaces
with the things. The last time I looked (version 300b2), there was a single
function left unimplemented which prevented the use of JavaButtons on a USB
adapter under linux. (non-java buttons were ok, serial port adapters were ok,
it was just the combination that didn't work). I don't yet know if that's
been fixed in the "new" (2004) version 300 library.

Trying to buy a JavaButton looks hard, much harder than it was when I got
mine. It probably requires talking to a sales rep. I got a starter kit that
included a DS1957 on a USB key fob, very nicely designed. The only part I can
see listed on their web site is the DS1955, which has like 8kB of ram (the
DS1957 has more like 150kB). The JavaButtons include cryptographic code, so
they require a license/export agreement, but it would have been nice if they
made it clear how you obtain such a thing.

Anyway, here are a handful of links, since their web site seems particularly
hard to navigate.


http://www.maxim-ic.com/products/ibutton/software/1wire/wirekit.cfm

http://www.maxim-ic.com/pl_list.cfm/filter/22
 list of iButton data sheets

http://www.maxim-ic.com/1-Wire.cfm
 regular ICs (not in a steel can) using the same protocol, usually TO-92

http://www.maxim-ic.com/products/microcontrollers/crypto_ibutton_license_application.cfm
 might be the entry point to buying a JavaButton, or maybe just one of their
 crypto iButtons

UPDATE: no, version 300 still does not support JavaButtons over USB. The
specific issue is that JavaButtons require a strong pullup to provide lots of
power while they're crunching away in the crypto routines. The USB adapter
can do this, but the Linux interface code doesn't know how to turn it on.
``lib/general/Link/USB_Linux/usblnk.c`` has a routine named
``hasPowerDelivery``, which currently reads::

 SMALLINT hasPowerDelivery(int portnum)
 {
    // Adapter supports it but not implemented yet
    return FALSE;
 }

Sigh.
