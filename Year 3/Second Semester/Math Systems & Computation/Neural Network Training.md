# Neural Networks
![](Pasted%20image%2020240505192053.png)
Neural networks are made of neurons (individual nodes that take on some value) and connected by synapses. Each synapse has a weight, indicating how much the starting neuron's value influences the ending neuron's value.
The input layer is the information being passed in, and the output layer is the **prediction** based on that input data. The network needs to be **trained** for this prediction to be meaningful; this is typically done using **supervised learning**, where inputs and expected predictions are provided, so that the weights of the network may be adjusted to bring the actual predictions closer to the actual predictions.

An example of the data used to train a neural network is MNIST:
![](Pasted%20image%2020240505192407.png)
This is a collection of labelled samples of hand-written digits. The images are 28x28 pixels and there are 60000 training images, plus 10000 extras to test your results with.

This data is input into the network as values on the input layer. In this example you might represent the intensity of one pixel as a single neuron in the input layer, making the input layer 784 neurons large.
The neurons in the subsequent layers then calculate their own values based on the impulses they receive from the previous layer. An example activation function is the **rectified linear unit** (ReLU):
![](Pasted%20image%2020240505200520.png)
**Left equation**: First the impulse is calculated. we take the value of every neuron in the previous layer and multiply it by the weighting on the connecting synapse. We then sum all of these inbound weights together