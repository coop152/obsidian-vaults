- Merged linux port and delete history into master, now it's all there
- Made a branch for autopurge
- First, gotta figure out how to ask the user for the auto-purge time
	- dg_num is looking useful, investigate
- Alright, got a quick copy of dg_num going in dg_duration, looks like this:
![](Pasted%20image%2020240220012000.png)
```tcl
set w [cal option ItemWidth]
    if [get_duration [ical_leader] {Autoclear Delay}\
            {Centimeters}\
            {Automatically clear out items in the delete history that are more than this many days old.
(Set to 0 to disable.)}\
            5 15 0 $w w] {
        cal option ItemWidth $w
        trigger fire reconfig
    }
```