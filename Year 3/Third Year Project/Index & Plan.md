# Week Journals
- [Proof of Concept Notes](Proof%20of%20Concept%20Notes.md) (Written in weeks preceding 25-9-23)
- [Week of 25-9-23(ish)](Week%20of%2025-9-23(ish).md)
- [Week of 2-10-23](Week%20of%202-10-23.md)
- [Week of 16-10-23](Week%20of%2016-10-23.md)
- [Week of 23-10-23](Week%20of%2023-10-23.md)
- [Week of 30-10-23](Week%20of%2030-10-23.md)
- [Week of 6-11-23](Week%20of%206-11-23.md)
- [Week of 13-11-23](Week%20of%2013-11-23.md)
- [Week of 20-11-23](Week%20of%2020-11-23.md)
- [Week of 27-11-23](Week%20of%2027-11-23.md)
- [Week of 4-12-23](Week%20of%204-12-23.md)
- [Week of 11-12-23](Week%20of%2011-12-23.md)
- Pretend you don't see this month and a half gap
- [[Christmas Holiday + Week 29-1-24]]
- [[Week of 5-2-24]]
- [[Week of 12-2-24]]
- [[Week of 19-2-24]]
- [[Week of 26-2-24]]
- [[Week of 4-3-24]]
- [[Week of 11-3-24]]
- [[Report writing notes]]
# Important Dates
- Code Freeze: Friday 15th March 2024 
- Report & Screencast Submission: Friday 12th April
# My initial porting plans
- [x] Create a visual studio project that compiles and functions a baseline amount
- [x] Achieve full functionality
- [ ] Fully strip out any Unix api usage, including microsoft compatibility functions
- [ ] (?) Refactor code to use modern C++
	- [x] Use chrono for time
	- [x] Replace most/all of the C style arrays with c++ style, most importantly strings (This includes the home-baked charArray class)
	- [?] use modern file APIs
	- [ ] Resolve code safety warnings (remove `_CRT_SECURE_NO_WARNINGS` from preprocessor definitions to see them)
- [ ] (?) Port from Tk to Qt (or similar), remove tcl completely from the project
- [x] ALTERNATIVELY, Find a way to reasonably package tcl/tk with the executable in a relocatable way

# random shit
- ~~i dont think alarms when combined with timezones ever worked? but richard seems to think that alarms failing during daylight savings is a new thing; maybe test on old version~~ He was right its a bug i introduced :(

# WHAT'S LEFT
- Porting
	- Fixing bugs (maybe unnecessary)
	- Using chrono for time
	- ~~Miscellaneous~~
	- ~~Creating the installer~~
	- ~~Backporting to linux~~
- Features
	- ~~Delete History~~
	- ~~Mass Delete~~
	- ~~Auto-cleanup~~
- Conclusion

When youve got something down for everything:
- Sort out references and the bibliography
- Go through and remove any contractions
- talk about feature requests in planning
- do a short section on the "switch calendar" button in Features
- maybe look at the comments in Analysis and add some more images
- add images and waffle more in the reflection/results bit
- add images to the installer section in porting
- add bit about updates to installer section


if you dont count code snippets your word count plummets. only remove if you go over and it puts you in the safe range.
```tex
% texcount stuff
%TC:envir minted [ignore] ignore
```
