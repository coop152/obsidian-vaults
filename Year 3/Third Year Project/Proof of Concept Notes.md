# Building with Cygwin
(write this later)
# Converting the build to use native Tcl/Tk
1. Download this native windows build of Tcl/Tk: https://www.irontcl.com/index.html
2. Rename `tcl86t.dll` to `libtcl8.6.dll` and put it beside the exe (the exe placed by `make install`, by default in `usr/lib`)
3. Rename `tk86t.dll` to `libtk8.6.dll` and put it beside the exe (same)
4. By substituting the Cygwin versions of the libraries for native ones, we have caused a crash: The program is looking for resources on the fake Cygwin drive, but the native libraries are using the actual Windows drive. This causes an error trying to find things in `/usr/local/`. Therefore, copy these files from the Cygwin drive to the real Windows drive:
	1. `C:/cygwin64/usr/local` -> `C:/usr/local`
	2. `C:/cygwin64/usr/share/zoneinfo/zone.tab` -> `C:/usr/share/zoneinfo/zone.tab`
	3. There may be more.

The program fails to save the calendar file; the default seems to resolve to `C:/.calendar` for some reason, which it obviously is not allowed to write to. This bug seems to originate from `startup.tcl.in` on line 38; it tries to put it in the home directory and bizarrely expands `~` to the C drive. (Cygnal actually fixes this - It correctly expands to `C:/Users/Kyle Coop/.calendar`.)

It seems like giving a windows path as the prefix to ./configure works fine; may be the way to go forwards? This works fine for ical's own files in `/usr/local` but it does not seem to work for the time zone file; maybe just fix this by itself by using an actual Tcl time zone API instead of reading a file (lol).

Alternatively, use [Cygnal](https://www.kylheku.com/cygnal/), a drop in replacement for the main Cygwin dll that makes it use actual windows paths instead of the fake root filesystem. Rename the dll to `cygwin1.dll` and put alongside the exe, the same as Tcl/Tk.
This way it actually looks in `C:/usr/share/zoneinfo/zone.tab` for the time zone file instead of in `C:/cygwin64`. Doesn't let me put it in an actually decent location, but its better than nothing.
(Also watch out for having the Cygnal dll in the current folder when compiling stuff - it messes with the compilation tools. It should be moved in next to the ical.exe in `/usr/share/bin` so its in effect when you run it from there.)

# Full process for making a standalone windows compile

1. Compile.
2. `make install`.
3. Create `C:/usr/`.
4. Copy all of ical's files from the Cygwin "drive" (`C:/cygwin64`) into this folder. The files are:
	- `/usr/local/bin/ical`
	- `/usr/local/lib/ical` (the entire folder and its contents)
	- `/usr/local/share/applications/ical.desktop` (this is almost definitely not required but I'm being thorough)
	- `/usr/local/share/man/man1/ical.1` (same as above)
5. Download [IronTcl](https://www.irontcl.com/index.html).
6. Extract `bin/tcl86t.dll` to `C:/usr/local/bin/libtcl8.6.dll` (note that the file was renamed)
7. Extract `bin/tk86t.dll` to `C:/usr/local/bin/libtk8.6.dll` (note that the file was renamed)
8. Extract `lib/tcl8.6/` to `C:/usr/local/lib/tcl8.6/` (the whole folder)
9. Extract `lib/tk8.6/` to `C:/usr/local/lib/tk8.6/` (the whole folder)
10. Copy these Cygwin dlls into `C:/usr/local/bin`, beside `ical.exe`:
	- `C:/cygwin64/bin/cyggcc_s-seh-1.dll`
	- `C:/cygwin64/bin/cygstdc++-6.dll`
11. Finally, download [Cygnal](https://www.kylheku.com/cygnal/), rename the file to `cygwin1.dll` and put it beside `ical.exe` like the other dlls.
12. It's That Simple!™

If you configure like this: `./configure --prefix="C:/usr/local"` then `make install` will do steps 3 and 4 for you.
# Current Bugs
~~Location: filesel.tcl, Line 150 and 154
Trying to open a file (Include Calendar, Link to Local File, etc.) makes ical freeze. This is because there is a loop that terminates when it reaches a filesystem path of "/", which cannot happen on Windows. Replacing "/" with "C:/" works, but is obviously not a very good solution. In fact, this entire file needs a once over to make it even slightly Windows compatible. Probably the only reason this custom file selector component exists is cause it was written for a version of Tcl/Tk so old that it didnt have a file selector built in. (investigate tk_getOpenFile and similar functions, which opens a native file picker dialogue.)~~ **SOLVED**

~~Location: main.C, Line 54
The program runs in a minimal text mode if it doesn't think you have an X server running. That is, if you don't have a `DISPLAY` env var defined or if you don't use the `-display` flag. Cause this clearly doesn't make sense on Windows, invert this check; assume graphical mode, and add a new cmdline argument for text mode.~~ **SOLVED**

~~Location: Somewhere in the toolchain
Currently, the generated makefile makes an exe with the console application flag set, so it opens a console window when ical is run. Cygwin will set the GUI application flag if you pass `-mwindows` when compiling the final .exe, and just editing the Makefile works; now to figure out how to make `./configure` produce that.~~ **SOLVED**

# Steps so far
- Compiled in Cygwin using an X server and the pretend Cygwin drive
- Compiled in Cygwin using native windows Tcl/Tk libraries and the pretend Cygwin drive
- Compiled in Cygwin using native windows Tcl/Tk libraries and Cygnal
- Made a new Visual Studio C++ project - use native Tcl/Tk but supplant unix libraries with win32