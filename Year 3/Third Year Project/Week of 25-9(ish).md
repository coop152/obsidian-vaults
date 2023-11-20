- Installed visual studio
- Created a project and added all of the .cpp and .h files
- Added each of the source folders to the include path
- Downloaded and extracted IronTcl into a folder, added the headers to the include path
- Began fixing files that used the unix APIs

# Fixed Files
`misc.cpp` - Function for getting the user's name needed converting. 
`calfile.cpp` - Many uses of unix `link` and `unlink` functions for moving, deleting and renaming files. Changed to use more meaningful ones (i.e. `remove` and `rename`), and replaced `access` (permissions check function) with the Microsoft provided porting function `_access`. **Still uses some Unix time stuff, because it uses Time.cpp**.