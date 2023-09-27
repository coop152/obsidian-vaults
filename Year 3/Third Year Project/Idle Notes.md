# Building with Cygwin
(write this later)
# Converting the build to use native tcl/tk
1. Download this native windows build of tcl/tk: https://www.irontcl.com/index.html
2. Rename `tcl86t.dll` to `libtcl8.6.dll` and put it beside the exe
3. Rename `tk86t.dll` to `libtk8.6.dll` and put it beside the exe
4. By substituting the Cygwin versions of the libraries for native ones, we have caused a crash: The program is looking for resources on the fake Cygwin drive, but the native libraries are using the actual Windows drive. This causes an error trying to find something in `/usr/local/`. Therefore, copy these files from the Cygwin drive to the real Windows drive:
	1. `C:/cygwin64/usr/local` -> `C:/usr/local`
	2. ``