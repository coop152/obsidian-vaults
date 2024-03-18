# Todos (at some point)
- [ ] Write the abstract, probably at the very end. it should summarise the entire project and list the key outcomes.
- [ ] figure out what's going in the appendix?
- [ ] Do I need a list of abbreviations?
- [ ] DO THE FUCKING SCREENCAST


# Steps to take (in order)
- [ ] Decide the names of the sections and put them in the report (this will generate the table of contents too). For example:
![](Pasted%20image%2020240318115805.png)
- [ ] Just write something, anything, in each section. No expectation of completeness or even presentability, just get the facts down. Make sure every single section has something before you start cleaning anything up. Dont even look for accurate information if you need it, just put a TODO marker and come back for it later.

# Sections
## Introduction
Give a bit of a surface level introduction to what ical is (i.e. its an old calendar program written by sanjay in the early 90s, for the various unix operating systems of the time)
State the aims of the project. that is, port the program to windows, and give the user a native windows-like experience, with an installer, a folder in program files...
Articulate how there wasn't much of an explicit plan, as much of the project involved investigating the existing code and discovering what needed to be changed/updated/added as I went along. More planning arised when I started adding features, but I still ended up doing quite a lot of code updating while adding said features so it was still mostly freeform.
Provide the motivation for writing a port. The example is the project supervisor, who likes this program and uses it on their linux machine, but cant use it on their windows machine. Enumerate the problems with their current options when there is no port:
- They can use their linux machine every time they want to access their calendar, which is obviously inconvenient and not reasonable
- They can ssh into their linux machine and inspect the contents of ical's calendar file manually or using a script (what he actually does currently), which is inconvenient. making modifications is very finnicky, and time based features such as alarms cant be used. You cant get a simple visual overview of your calendar like with the actual program, etc..
- They can use another calendar program on windows, which essentially forces them to stop using ical entirely (even on their linux machine) because their data won't be compatible.
- They can use a virtual machine (or similar) to run the program "on windows". This varies in badness; running a whole virtual machine (e.g. virtualbox) would be extremely inconvenient and badly integrated with the rest of the windows experience, and also inefficient as an entire linux OS would be running to support a single very lightweight program. They could run it through WSL, which provides an acceptable experience, but that also requires setting up a windows X-server (which is non-trivial) and still has all the trappings of a linux virtual machine (i.e. isolated file system that windows programs cannot read, strong visual discontinuity between virtualised programs and native ones)
- Then contrast with how great it would be to just have a native version.

FINALLY, provide a summarised roadmap to the following chapters of the report. This part will probably be pretty similar to other reports, so check the exemplars.

## Background
introduce what ical is in more detail, including how old it is, and thus how outdated much of the code is. Give context (i.e. this is before the C++ STL, so there is some code that is very questionable by modern standards)
Explain some concepts that will be heavily used later on. The C++ STL, including a description of templates, the included container types, the chrono time library, the file libraries... What tcl is and how it interops with C++, and why sanjay used two languages in this way to make his program.
Explore the different ways that a program can be ported. MinGW, cygwin, or visual studio, and the advantages/disadvantages of each.
Write a list of the problems that need to be remedied over the course of the project. Where relevant, reference the future sections that address these issues.
You might get some stuff to cite here. Or maybe not.

## Methodology or Design
Not too sure about this section. Pretend i had an ordered plan, something like:
- Get program to compile on windows
- Modernise code
	- Time handling (unix time api -> chrono)
	- Containers (homemade array class -> std::vector, homemade map class -> std::map)
	- housekeeping (using bool where int was used previously, replacing literal `0`s with `nullptr`s and `false`s where appropriate, making janky "generic" classes from before into templates)
- Package nicely 
- Introduce new features