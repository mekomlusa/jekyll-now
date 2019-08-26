# Feature Engineering for Machine Learning
## Ch.2: Fancy Tricks with Simple Numbers

* Most ML algorithms can only take numerical inputs. Feature engineering on numeric features: basic & critical.
* First sanity check: magnitude. Would a coarse granularity work instead of the actual value?
* Tricks to deal with counts, which may grow without limitation:
  * Binarization: turns the problem into 0/1, occurred/not occurred.
  * Binning: useful when the distribution is heavy-tailed
    * Fixed-width binning: Easy to compute, but may end up with multiple empty bins. 1) heuristics (e.g. group by age), 2) log for exponential-width binning, dividing by a constant for linear-width binning.
    * Quantile binning: adaptively positions the bins based on the distribution of the data. Quantile, decitile, etc.
* Log transformation: effective for heavy-tailed distribution. "It compresses the long tail in the high end of the distribution into a shorter tail, and expands the low end into a longer head."
  * It's not a one-time, save-all technique though. Need to inspect the relationship between the transformed feature and the target to see if model assumption holds.
* Power transformation: extension of log transformation
  * AKA "variance-stabilizing transformations": e.g. on Poisson, power transformation removes the dependency between variance & mean.
  * Box-cox: a simple generalization of both the square root transform and the log transform. Only works for positive data.
* Feature scaling: **doesn’t change the shape of the distribution**; only the scale of the data changes. Useful where a set of input features differ wildly in scale. For certain model, drastically varying scale in input features may lead to numeric instability.
  * Min-Max Scaling: squeezes (or stretches) all feature values to be within the range of [0, 1]
  * Standardization (Variance Scaling): scaled feature would have mean = 0 and variance = 1
  * L2 normalization: outputs will have norm 1. Not necessarily in the feature space; could be in the data space as well.
* Don't scale sparse data -> dense vector; huge computation burden on the classifier!
* Complex features, e.g. interactions (products of two features)
  * Do NOT need to curate manually for decision tree-based models; could help generalized linear models (to leverage logical AND relationship)
  * Easy to compute, but increases computation complexity (now has to consider second order features as well)
  * To account for the computational expense of higher-order interaction features: feature selection & handcrafted small sets. Both have their advantages & drawbacks.
* Feature selection: prunes away non-useful features in order to reduce the complexity of the resulting model.
  * Filtering: preprocess features to remove ones that are unlikely to be useful for the model. Computationally cheaper than the wrapper methods, but lack consideration for the underlying model. (May not select the right features for the model)
  * Wrapper methods: essentially using a subset of features and gradually growing. Expensive, but not doing pruning upfront. "The wrapper method treats the model as a black box that provides a quality score of a proposed subset for features. There is a separate method that iteratively refines the subset."
  * Embedded methods: feature selection is included as part of the model training process. Less powerful than wrapper, but not computationally expensive; better than filtering as it relies on the underlying model. A balance between computational expense & quality of results.
