- Working on making it more obvious when the calendar is in delete mode
	- [x] Make something on the window very obviously go red
	- [x] Make it very obviously say that it's in delete history mode
	- [x] Actually make the softdelete/restore button change label so you can tell what it does
- New bug discovered, making repeating appointments straight up crashes the program. Turns out this is where that infinite recursion warning comes into play!
![](Pasted%20image%2020240206112055.png)
- ~~Want soft-deleting to also put a copy in the clipboard - i guess the item object/handle shouldnt get deleted? look at how the regular perma-deleting works. probably why its a function on a calendar (in cal_tcl.cpp) and not on an item (in item_tcl.cpp)~~ fixed i think? check it