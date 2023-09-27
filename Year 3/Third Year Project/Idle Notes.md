# Building with Cygwin
(write this later)
# Converting the build to use native tcl/tk
1. Download this native windows build of tcl/tk: https://www.irontcl.com/index.html
2. Rename `tcl86t.dll` to `libtcl8.6.dll` and put it beside the exe
3. Rename `tk86t.dll` to `libtk8.6.dll` and put it beside the exe
4. This will crash. You need some more files according to the prefix you gave to `./configure` (default `/usr/local`): 