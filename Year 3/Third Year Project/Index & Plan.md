# Week Journals
- [Week of 25-9(ish)](Week%20of%2025-9(ish).md)
- [Week of 2-10](Week%20of%202-10.md)
- [Week of 16-10](Week%20of%2016-10.md)
- [Week of 23-10](Week%20of%2023-10.md)
- [Week of 30-10](Week%20of%2030-10.md)
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
- [x] ALTERNATIVELY, Find a way to reasonably package tcl/tk with the executable in a relocatable way

# How should deleting work
Check emails sent to Richard for a plan that I sent previously. Some fake mock-ups wouldn't go wrong! ALSO REMEMBER TO MAKE A BRANCH