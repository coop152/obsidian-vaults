## Requirements
The requirements given by the customer will be of two types:
- Functional requirements - Things that the application must do
- Non-functional requirements - How the application does things
A set of functional requirements can be fulfilled in many ways, with different sets of non-functional requirements.
For example, functional requirements for a housing search website may be:
R1. Find houses in a given area
R2. Limit searches to the correct type of house, by properties such as bedroom count
While non-functional requirements may be:
R3. Allow the user to pick the area by drawing a border on a map
R4. Show the results in a card-like format
But they could also be this instead, without changing the functional requirements:
R3. Allow the user to pick the area by entering their postcode and a maximum distance.
R4. Show the results on a map, with pins for each property
The requirements may be expressed with a simple list, but also through other means such as user stories. The purpose of these requirements is to elaborate on the desired scenarios and designs for the application before implementation begins. This is important because:
- It allows the full scope of the application to be described without being bogged down by the details
- It exposes misunderstandings in communication between the customer and the designer/developer
- It exposes flaws in the existing design (assuming there is one)
#### Example: Same requirements, different designs
![](Pasted%20image%2020230206115001.png)
## From requirements to design
#### Sketches
![](Pasted%20image%2020230206115047.png)
Very rough sketches of what the website ought to look like. Generally expresses a more general, non-specific view of the whole application (or the component with which the designer is currently creating). Quick and easy to make, which is good for iterating and finding issues, but not detailed enough to be a complete reference for the final implementation.
#### Wireframes
![](Pasted%20image%2020230206115405.png)
Similar to a sketch, but more particular about the layout. Generally used in a similar way to a sketch (i.e. to get a quick and messy representation of a design idea onto paper) but is more concerned about the actual layout and design of the page. Not as quick as a sketch, but more useful when it comes to translating into the final application.
#### Mockups
![](Pasted%20image%2020230206115635.png)
Polished designs for the application, intended to mimic the real thing. They have no functionality and are not usually created using the target technology; they represent the final design/designs that are to be implemented into the final application. Take significantly more time than a sketch or wireframe.
#### Prototypes
![](Pasted%20image%2020230206120028.png)
Implementations of the product with actual functionality and mostly final design. Take the longest to create, as they require combining the design with the required functionality and implementing the design concretely using the target technologies. Is the closest to the final product by a long shot.

## Guidelines for UI Design
There are many sets of guidelines an application may follow.
#### Ben Shneiderman's 8 Golden Rules
1. Strive for consistency
2. Seek universal usability
	- All computer abilities and ages
	- People who need assistive technology 
	- On different sizes of screen, e.g. mobile, desktop, TV
	- In many situations, e.g. sitting down, walking, while distracted
3. Offer informative feedback
	- When an error is encountered, tell the user what has gone wrong and how 
	- Suggest a next step
	- Use an appropriate tone
4. Design dialogues that bring closure
	- When a task has been completed, make it clear
5. Prevent errors
	- If possible, prevent users from submitting erroneous information. e.g, disallow dates in the past/future where appropriate, require @ symbols in emails.
6. Permit easy reversal of actions
	- Where possible, the user should be able to easily undo their previous actions
7. Keep users in control
	- Show the information that users requested, and nothing more.
	- NO FUCKING COUPON POPUPS
	- NO FUCKING SURVEY POPUPS
	- NO FUCKING COOKIE POPUPS THAT DEFAULT TO ALL SPYING ENABLED
8. Reduce short-term memory load
	- Minimise the amount of stimuli on any given screen or section, allowing the user to take in every element and remember what everything does easily
	- Cluttered designs are confusing
