# Week Journals
- [Week of 25th Sept(-ish)](Week%20of%2025th%20Sept(-ish).md)
- [Week of 2nd Oct](Week%20of%202nd%20Oct.md)
- [Week of 16th Oct](Week%20of%2016th%20Oct.md)
- [Week of 23rd Oct](Week%20of%2023rd%20Oct.md)
- [Week of 30th Oct](Week%20of%2030th%20Oct.md)
# Important Dates
- Code Freeze: Friday 15th March 2024 
- Report & Screencast Submission: Friday 12th April
# Things that need doing
- [x] Create a visual studio project that compiles and functions a baseline amount
- [x] Achieve full functionality
- [ ] Fully strip out any Unix api usage, including microsoft compatibility functions
- [ ] (?) Refactor code to use modern C++
	- [x] Use chrono for time
	- [ ] Replace most/all of the C style arrays with c++ style, most importantly strings (This includes the home-baked charArray class)
	- [ ] use modern file APIs
	- [ ] Resolve code safety warnings (remove `_CRT_SECURE_NO_WARNINGS` from preprocessor definitions to see them)
- [ ] (?) Port from Tk to Qt (or similar), remove tcl completely from the project
- [ ] ALTERNATIVELY, Find a way to reasonably package tcl/tk with the executable in a relocatable way

# How should deleting work
have another file .calendar.del