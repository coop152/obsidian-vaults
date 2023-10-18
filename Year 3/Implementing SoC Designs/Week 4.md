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

When converting this implementation to an actual concrete design, the design we should aim for is dictated by numerous factors:
- Speed
- Power
- Size
- Simplicity

Optimising for most of these requires skill and good judgement.
In the case of Speed, RTL simulations do give immediate feedback on cycle counts. 

# Stay Synchronous
There are good reasons to keep to a synchronous style when constructing a design:
- It simplifies the design by guaranteeing that time is the same everywhere. Given a state and some inputs, the next state can be determined unambiguously, and any glitches during switching are irrelevant.
- Toolchains are optimised for synchronous circuits
- Implementation technology is designed with synchronous circuits in mind (e.g. D-type flip-flops)

# Think Parallel
Hardware programming, unlike (most) software programming is implicitly parallel. Increasing the parallelism in a design typically:
- Increases speed
- Increases required resources
- Can *decrease* complexity
- May decrease power

You can achieve parallelism by:
- Subdividing the logic using pipelining (just make sure the stages are reasonably balanced)
- Pre-evaluating some logic in earlier stages, if possible (at the consequence of maybe making the structure messier)
- Finding more parallel logic expressions (For example, an adder's carries can be evaluated in parallel)

