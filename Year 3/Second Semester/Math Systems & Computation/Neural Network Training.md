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
- $L$ is the loss for this ANN and sample (which we want to minimise)
- $\hat{y}_k$ is the "ground truth" for class $k$, encoded as a one-hot vector (it equals 1 for the correct class and 0 otherwise)
- $P_k$ is the probability for class $k$ produced by the ANN.

## Optimisation vs training
In optimisation problems, the objective function only varies with the "design variables" that are given as an input. In the ANN case, those are the weights of the synapses.
However, the Loss of our network is a function both of the weights of the synapses **and** the input data. 
If we want to apply the gradient descent method described previously, we need to calculate the gradient of the loss for a single sample w.r.t. the weight parameters; if we do this individually for each sample, we can then combine the resulting optimal weights to get the average loss gradient across all samples in the training dataset.

Our objective function, which combines the Loss for every sample:
![](Pasted%20image%2020240505204748.png)
The gradient of the objective function which we use for gradient descent, which uses the gradient of the loss:
![](Pasted%20image%2020240505205711.png)
## Gradient Descent for ANNs
We will use gradient descent to train our neural network.
There are analytical solutions to find the gradient of the loss; we dont need to understand how these are found, but they exist so we can do gradient descent.
This is simply done as gradient descent has been done before. We calculate the gradient of the objective function, then we subtract it from our arguments (our weights, in this case):
![](Pasted%20image%2020240505210413.png)
There is a variable $\eta$ here called the "training rate" which provides some control over the training. It's essentially the step size.

Note that each gradient calculation requires the ANN to be evaluated on every single sample in the dataset; this is tractable if the sample count is small (say, 1000s of samples), but is prohibitive for larger counts (e.g. 1 million samples).

When researchers asked if there was a better way, they created:
## Stochastic Gradient Descent
![](Pasted%20image%2020240505210752.png)
It was discovered that estimating the gradient and then updating the weight based on a single sample performed very well, and approximated the true gradient descent if repeated over the entire training dataset.
This method, stochastic gradient descent, underpins the majority of current ANN training algorithms. (its stochastic cause the input presented is random.)
As long as you don't repeat samples, SGD approximates the true gradient well. The input data must be shuffled into a random order (e.g. don't have every input of a certain class at the same time, or it might not generalise properly to all classes)

Sampling the entire training set once is called an **epoch**. Further training may lead to overfitting, which can cause differences between training and testing accuracy but may still continue to improve the overall accuracy. (we don't need to worry about overfitting in this course.)

The benefit of this method is obvious; we don't need to put the entire dataset through the ANN just to calculate the gradient a single time.

There are some problems, however. Because we are only using a single sample at a time, some samples can introduce instability. This generally doesn't make the method unusable, but it often requires a smaller training rate. 
## Mini-Batch SGD
![](Pasted%20image%2020240505211706.png)
Researchers then found an even better way to perform gradient descent which fixes the previously mentioned problem with instability.

Instead of evaluating the whole dataset at once or only a single sample, we take batches of $m$ samples. This both provides the improved performance of SGD while smoothing out instability caused by variance in samples (because the batches will include samples from many classes).
The batch size is another (hyper-)parameter to the optimisation; small batch sizes tend to produce better generalising networks that are less stable, requiring smaller learning rates.

Notably, larger batch sizes can exploit parallelism in many-core architectures. If you process each sample in a batch in parallel then training times can be greatly reduced (it may be a good idea to set your batch size to equal your core count, or similar).

## Learning rate decay
![](Pasted%20image%2020240505214147.png)
A higher learning rate (or step size in the optimisation context) means larger steps taken in parameter space, meaning faster convergence. This is desirable during initial parameter updates, as it makes rapid progress towards an optimum.
Lower learning rates will improve stability and aid refinement around an optima, but they will slow the convergence down leading to longer training times.

A good strategy to set the learning rate based on this is **learning rate decay**. We pick a high initial rate, and decay to a lower rate during optimisation. This gives us the best of both worlds: a good initial convergence, then a stable refinement near the optimum.

We start with decay $\eta_0$, the initial learning rate. This will be used during epoch 0. $\eta_k$ then represents the learning rate for epoch $k$. In the equation, $\alpha = \frac{k}{N}$.
Setting the initial learning rate is challenging, and typically requires trial and error to find the best value.