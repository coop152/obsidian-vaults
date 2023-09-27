# Building with Cygwin
(write this later)
# Converting the build to use native tcl/tk
1. Download this native windows build of tcl/tk: https://www.irontcl.com/index.html
2. Rename `tcl86t.dll` to `libtcl8.6.dll` and put it beside the exe
3. Rename `tk86t.dll` to `libtk8.6.dll` and put it beside the exe
4. This will crash, because its looking for files with fake Cygwin paths on the real Windows drive. You need to copy some more files