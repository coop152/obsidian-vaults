# Important Dates
- Code Freeze: Friday 15th March 2024 
- Report & Screencast Submission: Friday 12th April
# Things that need doing
- [x] Create a visual studio project that compiles and functions a baseline amount
- [ ] Achieve full functionality
- [ ] Fully strip out any Unix api usage, including microsoft compatibility functions
- [ ] (?) Refactor code to use modern C++
	- [ ] Use chrono for time
	- [ ] use modern file APIs
	- [ ] Resolve code safety warnings (remove `_CRT_SECURE_NO_WARNINGS` from preprocessor definitions to see them)
- [ ] (?) Port from Tk to Qt (or similar), remove tcl completely from the project
- [ ] ALTERNATIVELY, Find a way to reasonably package tcl/tk with the executable in a relocatable way