Call this week the polishing up week.
- [ ] Update those READMEs to include accurate descriptions of building for windows AND Linux! remember, Linux building got a whole lot easier with the port.
- [ ] check that the files still transfer readily between Linux and windows without any problems! (i.e. check the line endings aren't fucking everything up and ruining my life again)
- [ ] Go through the code and really make sure you've removed all the legacy array types and old file API usages! that's one of the things I wanna say I did in the report!
- [ ] And check for any more TODOs that you might have forgotten about.

# Un-C-ifying Lexer
- Used this cool trick when converting Lexer to use std::string and fstream! https://stackoverflow.com/a/2602258
- Lexer now has the input buffer stored as a std::string instead of a cstring. still very janky on account of the use of the home-baked charArray for a temp thing.
- Modified all of the `GetId`, `GetNumber`, etc. lexer methods. They used to return a bool flag which says if it was a success or not, and then had an output variable with the result, but it seems odd to have an std::string out var. instead, i made them return the actual result and then gave them output bool variables that indicate success or failure! Makes sense to me.