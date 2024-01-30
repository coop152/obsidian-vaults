Spent this time backporting to Linux. This was while the delete history feature was in-progress on another branch, so I started by porting master:
- First I reintroduced all of the automake files from an old copy of the ical source code.
- CONTINUE WRITING THIS
- HAD TO FIX THE MAKEFILE STUFF CAUSE I MOVED WHERE THE LIBRARY IS FOR WINDOWS, AND HAD TO KINDA UNDO THAT ON LINUX
- HAD TO MODIFY STARTUP.TCL TO BE PLATFORM-DEPENDENT FOR THIS REASON

Todo:
- ~~Remove address sanitizer from makefile, see if adding to CPPFLAGS in configure will achieve the same effect~~ DONE, see below
- try and fix the TCL detection so the configure command is less arcane
	- Figured out how to eliminate the `CPPFLAGS=-I/usr/include/tcl8.6` 
- Default prefix possible? `~/.local` certainly makes sense.

---
Remember:
```sh
autoreconf
./configure --prefix=/home/kyle/.local --with-tclconfig=/usr/lib/tcl8.6 --with-tkconfig=/usr/lib/tk8.6 CPPFLAGS=-I/usr/include/tcl8.6
make install
```
It's gotta be installed, it looks in the prefix for the files!
Want address sanitiser? configure like this instead:
```sh
./configure --prefix=/home/kyle/.local --with-tclconfig=/usr/lib/tcl8.6 --with-tkconfig=/usr/lib/tk8.6 CPPFLAGS="-I/usr/include/tcl8.6 -fsanitize=address  
" LDFLAGS="-fsanitize=address"
```