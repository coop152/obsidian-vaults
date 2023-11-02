- Did an absolutely insane amount of re-typing to make every function that returned 0/1 actually return a bool instead.
- In the process of doing that, changed lots of `0` pointer literals to `nullptr`.
- Found bug where changing the calendar an item is in will not immediately update it's appearance. For example, it will not hide if the destination calendar is hidden and it will not take on the destination calendar's colour.

# Building Tcl/Tk statically from source
1. Download the source from the website (both tcl and tk) and extract them
2. Open a visual studio dev console
3. navigate to the source directory and then into the win folder (**you must use short paths because it doesn't like spaces in the path**, for example `C:\Users\KYLECO~1\source\TK86~1.13\win>`)
4. run `nmake -f makefile.vc OPTS=static,threads` and wait. Do this for both tcl and tk.

Now to link to the project:
1. Include some header files (i forgot which ones, check the branch)
2. Link to some lib files (check the branch)
3. add a `STATIC_BUILD` flag to the c++ compiler command (`/D "STATIC_BUILD"` in the compiler extra flags box)

This removes the requirement for the dll files (I think), but it still requires the entire lib folder so what even is the point?
Look into starpacks.