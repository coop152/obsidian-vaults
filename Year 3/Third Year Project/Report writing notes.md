# Todos (at some point)
- [ ] Write the abstract, probably at the very end. it should summarise the entire project and list the key outcomes.
- [ ] figure out what's going in the appendix?
- [ ] Do I need a list of abbreviations?
- [ ] DO THE FUCKING SCREENCAST
# Steps to take (in order)
- [ ] Decide the names of the sections and put them in the report (this will generate the table of contents too). For example:
![](Pasted%20image%2020240318115805.png)
- [ ] Just write something, anything, in each section. No expectation of completeness or even presentability, just get the facts down. Make sure every single section has something before you start cleaning anything up. Dont even look for accurate information if you need it, just put a TODO marker and come back for it later.

# Writing tips
- Always define abbreviations on first usage.
- If you add a figure, ALWAYS refer to it explicitly. Remember that latex is dogshit garbage and will frequently put your figure on a different page from the text you put it next to, because it fucking hates your guts and also cute dogs and babies and god.
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
- Fix bugs and broken functionality, opportunistically modernising code as I go
	- Time handling (unix time api -> chrono)
	- Containers (homemade array class -> std::vector, homemade map class -> std::map)
	- housekeeping (using bool where int was used previously, replacing literal `0`s with `nullptr`s and `false`s where appropriate, making janky "generic" classes from before into templates)
- Create an installer
- Introduce new features
- Backport to linux (follows from introducing new features)

## Results
Show the results of the project.
- some basic comparison screenshots of the program running on linux (original version, not that you can tell the difference by looking at the new version) vs on windows
- Screenshots showing the new features
- Screenshots of the installer, as well as the shortcuts it creates and the add/remove programs entry (to prove that it works like youd expect from a windows app)

## Evaluation
Evaluate the results according to some objective viewpoint. In this case, ive decided to send some questions to richard and asked him to answer them when he tests out the program. This, in addition to my own testing, should be enough for this section.

## Conclusions
Summarise the results from the evaluation. Have the aims of the project been achieved?. in the case of this project, the functional goals have been achieved but some of the code modernisation goals were not. 
List of things that could be improved:
- Did not entirely eliminate the homebaked container classes. Specifically, charArray was sticking around really badly in DateSet (i think that was the class), and there were some strange smallintset things? But they might not really count as containers.
- The program still has a very outdated appearance, thanks to a mix of tk's inherent uglyness and heavy use of custom controls. This could have been cleaned up significantly with time.
- With more intensive refactoring, every homebaked time/date class could have been removed and the chrono classes used directly. This isnt really that much of an issue but its still a thing, dunno if this should be mentioned
- Printing doesn't work on windows; it asks for a text device file name (which defaults to lpr, like youve got a parallel port printer), and the save to file option outputs postscript.
- There are also some inappropriate defaults that comically expose the age of the program; the print preview dialogue defaults to ghostview, the default browser for opening links is netscape. On modern linux you should just use xdg-open to open the default app, and im sure theres something on windows that does the same.
- 

Ideas for further work:
- More features:
	- Customisable sound for alarms (currently it is just the system bell, which plays the default ding noise on windows)
	- Some kind of online calendar syncing, ideally using a convenient service like dropbox, google drive, etc. This would be quite hard to implement but very useful
	- Allow the program to close to the system tray. It is quite strange for a windows program to run "in the background" by literally just running minimised and taking up space on your taskbar. Running in the system tray is much more typical.
- Further code modernization
	- Finish getting rid of the homebaked container classes
	- Potentially remove tcl/tk entirely and just use a native c++ interface library, like GTK or Qt. This would be a massive undertaking but would simplify the code an enormous amount. It would also make it impossible to write your own tcl scripts that interact with ical, like you currently can, but i absolutely refuse to believe that anyone is using that feature.


# Headings?
- Introduction
	- Background (what is ical)
	- Motivation (why am i porting it to windows)
	- Goals (Things i hope to achieve, and the ways i will achieve them (probably with forward links to the relevant sections))
- Planning
	- Methodology (make excuses for why I worked completely aimlessly)
	- Roadmap (summary of each step to be taken in order, with explanations of the order)
- Development
	- Porting and Maintenance
		- Initial Compilation (Making the visual studio project, getting the libraries, fixing those initial bugs using the windows api's linux compatibility functions. Make it clear what wasnt working yet, e.g. timezones)
		- Replacing obsolete code (reimplementing time code using chrono, reimplementing I/O using fstream, replacing the old container types)
		- Creating the installer
		- Backporting to Linux (remember that the makefile was a bitch to get right)
		- Miscellaneous (e.g. making that Array thing into a template instead of a hilarious macro hack, reading/writing in binary mode so that line endings are \\n on all platforms, all sorts of platform specific little baubles)
	- Features
		- Delete History
		- Automatic history cleanup (Autopurge)