# T-SNE reading notes

Original paper [link](http://jmlr.org/papers/volume9/vandermaaten08a/vandermaaten08a.pdf?source=post_page---------------------------)

* Stochastic Neighbor Embedding (SNE): Hinton and Roweis (2002)
  * Unlike PCA, SNE converts high-dimensional Euclidean distances between data points into **conditional probabilities** (a different similarity measurement).
  * Intuition: if x_i is similar to x_j, x_j is more likely to be picked as x_i's neighbor (hence higher probability p_{j|i}). P_{j|i} = the similarity of datapoint x_j to datapoint x_i.
  * Assume that similarity between the lower dimensional counterparts y_i and y_j could be also calculated in the same manner. Therefore we have Q_{j|i} = the similarity of map point y_j to map point y_i.
  * In a perfect world, P_{j|i} = Q_{j|i}. SNE goal: minimizes the discrepancy between P_{j|i} & Q_{j|i}. Specifically: it minimizes the sum of Kullback-Leibler divergences over all data points (conditional probabilities) using a gradient descent method.
  * Variance sigma_i is used in P_{j|i} (the original high dim representations). It's not the same across the board; SNE performs a binary search to identify sigma_i given the **perplexity** specified by the user. Perplexity = a smooth measure of the effective number of neighbors.
  * To avoid poor local minima & speed up optimization: a relatively large momentum term is added to the gradient.
  * SNE adds Gaussian noises to the map after each iteration. The noises decay gradually; similar to simulated annealing, also to avoid poor local minima.
* Problem with SNE:
  1. cost function is difficult to optimize
  2. the “crowding problem”
* t-SNE improvements:
  * New cost function: symmetrized version of the SNE cost function with simpler gradients; in the lower dimensional space, use student-t instead of Gaussian to compute similarity.
  * To overcome the crowding problem & the optimization limitation: it employs a heavy-tailed distribution in the low-dimensional space.

* Symmetric SNE: it minimizes a single Kullback-Leibler divergence between a *joint probability* distribution, P, in the high-dimensional space and a *joint probability* distribution, Q, in the low-dimensional space. p_{ij} = p{ji} and q_{ij} = q{ji}, hence "symmetric". Note that q_{ij} and p_{ij} are defined differently; p_{ij} = \frac{p_{j|i} + p_{i|j}}{2n} so that even an outlier would have significant contribution to the cost function. It has a simpler gradient than the original asymmetric version.
* The "crowding" problem: occurs when attempting to map data that are of higher intrinsic dimensions into 2-D; points are forced to map into the center, thus impeding the formation of clusters.
* T-SNE converts distances into probabilities using a Gaussian distribution in the high-dimensional space, while student-t distribution is used in the low-dimensional map.
* Advantages:
  * Easier to optimize cost function
  * No simulated annealing needed: t-SNE introduces long-range forces in the low-dimensional map that can pull back together two (clusters of) similar points that get separated early on in the optimization (also helps with finding good local optima)
* Tricks to optimize T-SNE (use either one of them):
  * Early compression: forces the map points to stay close together at the start of the optimization. Achieved by adding a L2-penalty term to the cost function. Magnitude and iteration are set by hand.
  * Early exaggeration: multiple all p_{ij} by a term (e.g. 4). Large q_{ij} for large p_{ij} -> large spaces between clusters.
