Call this week the polishing up week.
- [x] Update those READMEs to include accurate descriptions of building for windows AND Linux! remember, Linux building got a whole lot easier with the port.
- [x] check that the files still transfer readily between Linux and windows without any problems! (i.e. check the line endings aren't fucking everything up and ruining my life again)
- [x] Go through the code and really make sure you've removed all the legacy array types and old file API usages! that's one of the things I wanna say I did in the report!
- [x] And check for any more TODOs that you might have forgotten about.
- [ ] Remove charArray from DateSet, which is pretty much the last holdout. then remove Array<> entirely (which will also require removing an occurrenceArray or something like that) (Probably wont be able to do this in time, i tried and it was just so much work)
- [ ] Got all the licenses in check (i believe, its kinda hard to tell if the program itself is under GPLv2 or if its all copyrighted, cause the files in the old README dont actually exist and everywhere seems to say it's GPL without actually indicating where they got it from)
# Un-C-ifying Lexer
- Used this cool trick when converting Lexer to use std::string and fstream! https://stackoverflow.com/a/2602258
- Lexer now has the input buffer stored as a std::string instead of a cstring. still very janky on account of the use of the home-baked charArray for a temp thing.
- Modified all of the `GetId`, `GetNumber`, etc. lexer methods. They used to return a bool flag which says if it was a success or not, and then had an output variable with the result, but it seems odd to have an std::string out var. instead, i made them return the actual result and then gave them output bool variables that indicate success or failure! Makes sense to me.
	- It, in fact, did not make sense. I didnt even think to actually check the calling code, but it did things like this:
		```cpp
    if (! lexer->SkipWS() ||
        ! lexer->GetId(keyword)) {
        return false;
    }
		```
	- So I undid all that work and just stuck with the char pointers. the actual thing i wanted to do was eliminate the charArray tmp, which i could do by replacing that with a std::string and giving out its c_str. Eliminating charArray entirely is a different matter, which will be in a different section...
- It now uses fstream for reading the input file, which massively simplifies the constructor, notably.
# Eliminating charArray
- First I started with Lexer, since that's where I was already from the last section.
- I replaced Lexer's temp buffer with a std::string, and then removed Lexer::PutString, which entirely stripped charArray from the class.
- PutString was still used in Item and OptionMap, in their Write methods. Thankfully this was easier to resolve than i thought: earlier in the project, i had rewritten most of these Write methods across the codebase into std::string conversion methods. Most of them were unused - i just got lazy on the optionmap one and implemented a string conversion method that ran the old Write method on a charArray then converted it. The Item one wasn't even used, so that was easily removed, and the OptionMap one was straightforwardly implemented. I could then remove both of them, allowing everything to work properly without Lexer::PutString.
- Unfortunately, DateSet makes very heavy use of charArray and I was strapped for time, so i couldn't entirely remove the homemade Array type.

# Removing the final fopen
- After removing the usage of the C file api in Lexer, i found one final usage in misc.cpp. It was a simple copy_file function, which was easily converted to using fstream. There is no more use of the old C api in the program now.