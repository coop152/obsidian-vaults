- Added a feature to switch the current main calendar file. 
- Also, switches two of the lists on the Calendar class to use std::vectors. This didn't end up actually being useful cause it was an artifact of my first try at the undo button (you can copy vectors) but i left it anyway.

- Refactored the home-baked growing array class to not be written entirely in a macro and actually be a template (lol)