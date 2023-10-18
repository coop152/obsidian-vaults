# Design trade-offs
Given a specification, how do you decide on the perfect implementation?
Answer: There is no such thing. There are many ways to make something work, and many more ways to make something that doesn't work.
When picking an implementation, we want to consider it's qualities in this top-down order:
- Algorithms
- Architecture
- RTL
- Practicalities
	- Power supply
	- Cooling
	- Pad limitations
	- etc.

Algorithms refers to the actual algorithm used; if you can use a Fast Fourier Transform (for example) instead of a Discrete Fourier Transform then the difference in speed that makes will trump the difference of anything else.
Architecture refers to the general design (e.g. how smaller parts connect and communicate, etc.)