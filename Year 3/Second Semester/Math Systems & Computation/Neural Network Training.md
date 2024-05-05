# Neural Networks
![](Pasted%20image%2020240505192053.png)
Neural networks are made of neurons (individual nodes that take on some value) and connected by synapses. Each synapse has a weight, indicating how much the starting neuron's value influences the ending neuron's value.
The input layer is the information being passed in, and the output layer is the **prediction** based on that input data. The network needs to be **trained** for this prediction to be meaningful; this is typically done using **supervised learning**, where inputs and expected predictions are provided, so that the weights of the network may be adjusted to bring the actual predictions closer to the actual predictions.

An example of the data used to train a neural network is MNIST:
![](Pasted%20image%2020240505192407.png)
This is a collection of labelled samples of hand-written digits. The images are 28x28 pixels and there are 60000 training images, plus 10000 extras to test your results with.