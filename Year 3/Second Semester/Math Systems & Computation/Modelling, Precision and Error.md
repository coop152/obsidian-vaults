# Mathematical Modelling
Mathematical models are used to make predictions about the world, where real-life testing may be prohibitively expensive, difficult, or even impossible. For example:
- In aerospace, estimate the effect of Mars' atmosphere on a spacecraft
- In Meteorology, predicting the weather
- In Finance, estimating future stock prices

Computers make modelling possible. They allow solutions to problems to be found even where no analytical solution is known, and for designs to be tested in many environments without needing to be manufactured.

As scientists and engineers study real world problems, they analyse their results and produce sets of fundamental laws, which encapsulate the collective thinking of their field. These laws are continually updated as new experiments are performed and the pool of available data grows. These insights and formulations can be combined to make *mathematical models*. For example, the fundamental laws of physics form a mathematical model of physics, which could be implemented in a computer to form a variety of physics simulations.
![](Pasted%20image%2020240201111628.png)
These simulations can then be used to perform "sensitivity analysis", where the the model is tested under various input parameters and the results are used to make decisions. For example, you might:
- Simulate the spread of a disease under many different conditions, so that you can find the one where the disease spreads the least and enact these conditions in real life, lessening the spread of a real disease.
- Simulate the aerodynamic performance of many car chassis designs, so that you can choose the one that performs the best and manufacture it for a real car.

The general form of a mathematical model is
$$y = f(x, k, i)$$
where:
- $y$ represents the dependent variables, those variables which reflect the state of the system through their dependence on it
- $x$ represents the independent variables, the dimensions on which the model is evaluated (e.g. space, time)
- $k$ represents the parameters, the input values which can be modified to test the model on different subjects (e.g. material properties, object shapes, reaction coefficients)
- $i$ represents the *forcing function*, the stimulus that prompts the model to perform whatever interesting action that you want to investigate (e.g. current or load on a circuit, initial force in a physics simulation)

In a real model, the inputs and outputs are likely to be large systems of equations; Aircraft structural analysis models contain billions of variables. This is where computers and numerical methods are required: They enable you to solve large systems of equations which would be impossible to solve analytically, or by hand. Even for powerful computers, these models regularly take weeks or months to execute simulations, and they consume massive amounts of energy as they do so. In this situation, the performance and accuracy of the implementation of the model is crucial.

This unit's sections fit into this workflow here:
![](Pasted%20image%2020240201113045.png)
We are concerned with how to implement a performant mathematical model, while being certain that our optimisations do not introduce any errors. Then when we have an accurate and fast model, we are concerned with how we might use our results to optimise our inputs.

# Accuracy and Precision
**Accuracy** is how closely computed/measured values lie to the true value.
**Precision** is how closely computed/measured values lie to *each other*.
Taken together, Accuracy and Precision represent the **error** in a solution or method.
![](Pasted%20image%2020240201113831.png)
## Absolute Error
Numerical errors arise when approximations are used to represent exact mathematical operations or quantities.
$$\text{True value = approximation + error}$$
The **error** $E_t$ (aka the **absolute error**) represents the difference between the true value and the approximation.
$$E_t = \text{true value - approximation}$$
For example, we measure the length of a bridge and the diameter of a wheel. We measure the bridge as 9999cm and the wheel as 29cm. The true values are actually 10000cm and 30cm respectively. We calculate the absolute error, and in both cases we get 1cm. But is this a useful quantity? Clearly the 1cm difference is more significant on the wheel than the bridge. Absolute error does not take into account the magnitude of the quantity.
## Relative Error
Relative error ($E_r$) includes the magnitude of the evaluated quantity by normalising the absolute error by the true value:
![](Pasted%20image%2020240201114804.png)
This can easy be converted into the percentage relative error, $\epsilon_t$.
Note that the errors defined up to now are "true" errors, as they all use the known true value. This is in contrast to "approximate" errors, where the true value is not known and only an estimate can be used.
Repeating the bridge/wheel example, we see that the relative error is a much more representative value:
![](Pasted%20image%2020240201115102.png)
## Round-off Error
Computers cannot exactly represent all numbers. For example, $\frac{1}{30} = 0.03333\dots$
Other values include $\pi, e, \sqrt(2)$.
Computers can only represent approximations of these values - the discrepancy between these approximations and the true values is known as the **round-off error**.
## Truncation Error
Error can be introduced inherently by certain methods or techniques, for example approximating functions. For example, the exponentiation function can be approximated by the Maclaurin series:
![](Pasted%20image%2020240201115459.png)
When taken to the limit of $n=\infty$ there is no error, but in practise a finite number of terms must be used and only a certain level of accuracy can be achieved. When we only include a finite number $n$ terms, the higher order terms are **truncated**. 
As an example, let's use the Maclaurin series to approximate $e^{0.25}$ to 3 significant figures.
Compare to the true solution (to 14 sig.fig.) 1.28402541668774:
![](Pasted%20image%2020240201115915.png)
So having some truncation error needn't be a problem; We can still reach a suitable result as long as we are careful with our precision.
## Miscellaneous Errors
- **Formulation error** - Where an incorrect choice is made in the modelling technique, resulting in errors or restricting the possible inputs to the model
- **Blunders** - Simple human error, at points where human input is needed (e.g. unit conversion mistakes)
- **Data** - If there is uncertainty in the data that the model is based on, even a perfectly evaluated model will cause errors. (e.g. a high proportion of random fluctuations in measuring equipment, a systematic bias in the measurement methodology)

