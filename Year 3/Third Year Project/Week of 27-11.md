- Switched `CalFile` to use modern C++ file APIs, as well as `std::strings`, `filesystem::path`s
- This required adding lots of methods to other classes to deal with `std::string`s:
	- `Calendar::Write` which takes an `ofstream` instead of a `FILE*`
	- `std::string` conversion operator for `DateSet`, `Item`, `Appointment` and `OptionMap`
	- new function `Lexer::EscapeString` which behaves like `Lexer::PutString` but returns an escaped `std::string` instead of modifying a given `charArray`
- Note that Item and Appointment got proper new implementations, while DateSet and OptionMap just got quick wrappers that call the old methods and convert the result. This can be resolved later
- There is still some use of Unix file APIs (in the form of stat for checking permissions and such), which should be removed

- There is now a "functional" delete item button which removes an item and puts it in the deleted items list. This properly saves to disk, and is loaded along with the calendar.
- There is also a temporary "restore all deleted items" button just to test that items can be restored.

- This week is when I actually figured out how the Tcl/C++ object interface works. That is, `Item_Tcl` represents a **Tcl handle on a C++ object**, so if you delete an `Item` then you have to delete the corresponding `Item_Tcl` too, or else the Tcl will try to reference the deleted `Item`. By delete I literally mean `delete item;` in the C++, because it removes the handle from the Tcl in the destructor and nowhere else (lol).
- Vitally, deleting the handle object (`Item_Tcl`) DOES NOT delete the Item, it essentially just hides it from the Tcl. Because the Tcl displays every item it knows of, this is a nice and easy way to hide a certain group of items from the user without having to actually implement it in Tcl (which easily makes up for the slight jank factor)
- Also the Tcl has no idea when you change something so you need to invoke one of the triggers, either on the Tcl side with `trigger fire action $item` or in the C++ with `trigger(tcl, "action", item->handle())`. Leave off the item if its not a trigger for a certain object (e.g. `trigger fire flush` or `trigger(tcl, "flush")` to re-check and re-draw every item in the calendar).