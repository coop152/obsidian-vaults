K-means performs **hard clustering**. This means that each point is put into a single cluster with no leeway - you cannot have a point in two overlapping clusters, and there is no concept of a point being "strongly" or "weakly" in a cluster. For example, this clustering is not possible with k-means:
![](Pasted%20image%2020240228155103.png)
# Gaussian Mixture
Gaussian Mixture is a **probabilistic** clustering method. In contrast with the hard clustering of k-means, Gaussian Mixture (and indeed all other probabilistic models) give a **soft clustering**. Instead of just assigning each point to a cluster, we model each cluster as a Gaussian distribution. Any given point can lie in any number of clusters, and with a certain weight for each cluster depending on how close it is to the cluster's mean. 
This method makes use of **multivariate normal/Gaussian distributions**.
## Multivariate normal distributions
We already know of a **univariate** normal distribution; the usual 1-D normal distribution. There are equivalent multi-dimensional/multivariate normal distributions, for example: