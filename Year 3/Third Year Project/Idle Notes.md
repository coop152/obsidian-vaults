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
(Also watch out for having the Cygnal dll in the current folder when compiling stuff - it messes with the compilation tools. It should be moved in next to the ical.exe in `/usr/share/bin` so its in effect when you run it on the PATH.)
