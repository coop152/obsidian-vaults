Spent this time backporting to Linux. This was while the delete history feature was in-progress on another branch, so I started by porting master:
- First I reintroduced all of the automake files from an old copy of the ical source code.
- Of course this didn't compile, so I got to work modifying the code. This wasn't actually much work, and mostly constituted of looking at errors, finding a windows API function being used, and then wrapping things in
```C 
#ifdef _WIN32
WindowsApiFunctionWithLongDescriptiveName()
#else
linuxapifunc()
#end
```
- Now it compiled but it was doing the same blank-box thing that happened on windows when that first began compiling. I already knew at this point that it was because it couldnt find startup.tcl. I started by modifying main.c: on linux the makefile passes the location of the tcl files as ICALLIBDIR, so on windows i just defined this as `./tcl` beside the executable.
- This kind of worked and allowed startup.tcl to be found on Linux, but I forgot that i had moved all of the other tcl files into a `tcl` folder for the sake of organisation. Looking in ICALLIBDIR, all of the tcl files apart from startup.tcl had been put into a `tcl` subfolder, preventing startup.tcl from finding them.
- To fix this I did some (very annoying) modifications to the Makefile which make it move the files from the subfolder into the expected location. 
- I also had to modify startup.tcl itself. Previously on Linux, the makefile used `sed` to insert ICALLIBDIR into startup.tcl so it could find the libraries. I removed this on windows and hardcoded the library path as `./tcl` since the windows build tools couldn't do this substitution. In order to have both of these behaviours working at once, I wrote this cursed little piece of code:
```tcl
# set library path for both platforms
global tcl_platform
if [string equal $tcl_platform(platform) windows] {
	# tcl files are beside the executable on windows
	set ical(libparent) {.}
	set ical(library) {./tcl}
} else {
	# tcl files are in the directory set during compilation on linux
	# TECHNICALLY when compiling in windows these values are not substituted
	# so on windows this is a syntax error
	# but it's fine, its an interpreted language and we take a different branch (lol)
	set ical(libparent) {@pkglibdir@}
	set ical(library) {@icallibdir@}
}
```
- So on Linux the substitution is done as before and the second branch is taken, and all is well.
- On windows the second branch actually has a syntax error, as the substitution isn't done. It's fine though, we always take the first branch and tcl is an interpreted language so we never hit the syntax error (great coding, lol).
- I also made some improvements to the build process, which was clearly suffering from being written in the mid 90s. Previously the ./configure step failed to find tclConfig.sh on pretty much any platform. The code to find these files was implemented from scratch and just didn't work. I replaced this code with some helpful autoconfig functions that are included with the tcl standard library (?): `SC_PROG_TCLSH(), SC_PATH_TCLCONFIG(), SC_PATH_TKCONFIG()`. These functions essentially do what the old code did, but they *actually find* tclConfig.sh.
- The old build process also strangely forgot to pass the tcl header files to the C compiler, even though it exposed the path to the makefile. It just didn't use it. Easy fix, and now configuring is as easy as `./configure --prefix=/home/kyle/.local`!

Installation instructions:
1. Run `autoreconf -ivf`.
2. Run the newly created `./configure`: `./configure --prefix=/home/you/.local`
3. `make install`. You have to install it: ical looks for the tcl libraries in the prefix so it won't work if you just run the executable in the folder! (Watch out for compiling a new version but not installing; you'll be using the old tcl libraries but a new executable!)
---
Remember:
```sh
autoreconf -ivf
./configure --prefix=/home/kyle/.local
make install
```
It's gotta be installed, it looks in the prefix for the files!
Want address sanitiser? configure like this instead:
```sh
./configure --prefix=/home/kyle/.local CPPFLAGS=-fsanitize=address LDFLAGS=-fsanitize=address
```