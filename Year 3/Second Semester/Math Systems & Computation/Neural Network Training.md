# Artificial Neural Networks
![](Pasted%20image%2020240505192053.png)
Neural networks are made of neurons (individual nodes that take on some value) and connected by synapses. Each synapse has a weight, indicating how much the starting neuron's value influences the ending neuron's value.
The input layer is the information being passed in, and the output layer is the **prediction** based on that input data. The network needs to be **trained** for this prediction to be meaningful; this is typically done using **supervised learning**, where inputs and expected predictions are provided, so that the weights of the network may be adjusted to bring the actual predictions closer to the actual predictions.

An example of the data used to train a neural network is MNIST:
![](Pasted%20image%2020240505192407.png)
This is a collection of labelled samples of hand-written digits. The images are 28x28 pixels and there are 60000 training images, plus 10000 extras to test your results with.

## ANN Forward Pass
The training data is input into the network as values on the input layer. In this example you might represent the intensity of one pixel as a single neuron in the input layer, making the input layer 784 neurons large.
The neurons in the subsequent layers then calculate their own values based on the impulses they receive from the previous layer. An example activation function is the **rectified linear unit** (ReLU), seen on the right here:
![](Pasted%20image%2020240505200520.png)
**Left equation**: First the input impulse to the neuron is calculated. we take the value of every neuron in the previous layer and multiply it by the weighting on the connecting synapse. We then sum all of these inbound weights together.
![](Pasted%20image%2020240505201130.png)
**Right equation**: We take the calculate input impulse and then pass it through the activation function, in this case ReLU. this function simply clamps the result to being positive; if it is already above 0 then it is unchanged, but if it's below zero then it becomes zero.

To perform the entire forward pass, we put our input data on the input layer, and then perform these forward calculations to get the value of each hidden layer and then eventually the output layer.

## ANN Prediction
After performing the forward pass, the result is on the output layer. For classification problems (i.e. the coursework one) it is common to use **one-hot encoding**, where each class in the dataset is represented by a single output neuron.
To evaluate the prediction we need to transform the output neuron activations into some probability distribution. One way is using the **softmax** function:
![](Pasted%20image%2020240505201814.png)
(Ignoring the exponentiation, ) this takes the activation of each neuron and divides it by the sum of all of the activations, providing a probability. We then declare that the predicted class is whichever among the calculated values is the maximum.

## Optimising prediction accuracy
We need to train our ANN, by optimising the weights to maximise the prediction accuracy.
An optimisation algorithm requires an objective function to evaluate which characterises the problem; for an ANN this is also called the Loss function. In supervised learning, we evaluate this loss function using the current ANN prediction as well as the ground truth for that particular sample.
A common loss function is **categorical cross entropy loss**:
![](Pasted%20image%2020240505202231.png)
Where:
- $L$ is the loss
- $\hat{y}_k$ is the "ground truth" for the given sample $k$, encoded as a one-hot vector (1 for the correct class and 0 otherwise)
- $P_k$ is the probabilities produced by the ANN.

Note that this function operates with multiple samples. That's what the summation is over.
