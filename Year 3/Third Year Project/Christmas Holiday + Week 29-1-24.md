Spent this time backporting to Linux. This was while the delete history feature was in-progress on another branch, so I started by porting master:
- First I reintroduced all of the automake files from an old copy of the ical source code.
- CONTINUE WRITING THIS
- HAD TO FIX THE MAKEFILE STUFF CAUSE I MOVED WHERE THE LIBRARY IS FOR WINDOWS, AND HAD TO KINDA UNDO THAT ON LINUX
- HAD TO MODIFY STARTUP.TCL TO BE PLATFORM-DEPENDENT FOR THIS REASON
- 