- Add keyboard shortcut and option in context menu for deleting to the history
- Added "View Delete History" option in File menu that displays deleted items. You can't restore them individually yet, but you can do everything you could do with a normal item to them.
- As of yet not done:
	- Change the colour and title of the window (to do this i probably need to store the historyMode in the Tcl, instead of just flipping it in the C++)
	- Probably wanna hide LOTS of menu options and rename some (e.g. replace Delete with Expunge and make it permanently remove, don't allow copying and pasting perhaps? but maybe not, ask richard)