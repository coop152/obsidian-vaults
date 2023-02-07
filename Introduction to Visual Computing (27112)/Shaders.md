In old versions of OpenGL, the graphics pipeline was **fixed**. All vertices ran through a fixed sequence of algorithms, and the only part that a programmer could configure was certain parameters going into these algorithms.
![](Pasted%20image%2020230207141307.png)
But newer versions have a **programmable pipeline**, which (while still somewhat fixed in its order and some of its functions) can be programmed at certain points using a shader language called **GLSL**. These shader algorithms run once on each vertex, just like in a fixed pipeline.
![](Pasted%20image%2020230207141415.png)
## Vertex Shaders
All shapes are made of vertices. A vertex shader receives each of these vertices in turn, and does processing on each one. In GLSL, the vertex shader must, at an absolute minimum, set a value of `gl_position` (which is a vec4) for each vertex, which is where it will appear on screen. It is very likely that a vertex shader will do more than this, however. The output of the vertex shader will be passed along to the next step in the pipeline, eventually reaching the fragment shader.
In three.js, shaders are made by creating "materials". These shaders can be changed.

## Fragment Shaders
The fragment shader determines how an object with some vertices shall be coloured. A fragment shader, at an absolute minimum, must set a value for `gl_FragColor` (which is a vec4) which represents the final colour of that fragment.

## Examples: Simplest Possible Shaders
Simplest possible `vertexShader`:
```C
void main () {
	gl_Position = projectionMatrix *
					modelViewMatrix *
					vec4(position, 1.0)
}
```
three.js also provides light data, vertex colours, vertex normals, and a variety of other helpful values alongside the projection and modelView matrices.
After the `vertexShader` has positioned all of the vertices, the resulting triangles are **rasterised**. This converts the triangles into pixels, which are the input for the `fragmentShader`. 
![](Pasted%20image%2020230207143143.png)
The `fragmentShader` takes these pixels and applies colour to them.
Simplest possible `fragmentShader`:
```C
void main () {
	gl_FragColor = vec4(1.0, 0.0, 1.0, 1.0);
}
```
This shader simply sets all pixels to solid purple. This is not the only way to declare shaders, but that comes later.

## Where does the input data come from?
The input data (such as light information, camera information, material information) comes in multiple forms:
- **Uniforms** - Values that are declared with a value and assigned to the vertex or fragment shader.
	- Stay constant throughout the frame, i.e. same for all vertices in one rendering but may change over time
	- Usually single values, e.g. light positions, material colours, material shininess...
- **Attributes** - Values that are only passed into the vertex shader.
	- Applied to single vertices, and only available to the vertex shader.
	- May be an array of attributes, e.g. one value per vertex
- **Varyings** - Values passed from the vertex shader to the fragment shader, with the value usually computed in the vertex shader.
	- Variables declared in `vertexShader` and then shared with `fragmentShader`
	- A way of passing data from `vertexShaders` to `fragmentShaders
	- To declare a varying variable, it must be declared in both shaders separately with an identical name and type

