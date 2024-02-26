- Made the dialog actually set the value in the calendar object (specifically, in the option map), which saves it to the calendar file nice and tidy alongside the other options.
- Finished cleaning up the code for the dialog window itself

As for deleting a range of items, `cal listing` (or `cal_listing()` in the C++) seems like the bet. It takes 5 arguments from the tcl:
- A start date
- A finish date
- a variable name `iVar` that an item (handle) will be put in
- a variable name `dVar` that a date will be put in
- a tcl function

 It will find all items in the range from start date to finish date, and for each one it will
1. Assign an item handle to the given tcl variable `iVar`
2. Assign a date to the given tcl variable `dVar`
3. Run the given tcl function (where you can now use the item and date variables to do as you wish)
Here is an example