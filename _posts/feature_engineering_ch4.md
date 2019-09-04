# Feature Engineering for Machine Learning
## Ch.4: The Effects of Feature Scaling: From Bag-of-Words to Tf-Idf

* TF-IDF = term frequency-inverse document frequency. Scaled BOW by the number of documents a word appears in.
  * Intuition: it makes rare words more prominent and effectively ignores common words.
  * Closely related to the frequency-based filtering method, but without the hard cutoff thresholds.
  * An example of feature scaling
  * Could altogether eliminate uninformative words; stretching or compressing words
* **Important**: always fit the scaler on the training set, then use it on the training and the testing set. (In other words: don't do `fit_transform` on both training & testing sets)
* `GridSearchCV` in `scikit-learn` for hyperparameter tuning
* The quality of the trained linear classifier directly depends upon the null space and the column space of the data matrix.
  * Large column space: littler linear dependency between features -> good!
  * Large null space: too many "novel" data points that cannot be formulated as linear combinations of existing data -> bad.
* Rank deficiency: happens when the column space is NOT full rank (i.e. linear dependency exists between features).
  * Rank-deficient row space and column space lead to the model being overly provisioned for the problem.
  * In other words: harder to pin down a solution.
* Feature scaling: normally does **not** change the rank of the column space/null space. Say that we scaled a feature to be two times larger. We can always recover back to its original state by shrinking the transformed features two times smaller.
  * ! - cannot recover the original linear combination if the scaler = 0
  * L2 normalization would never compute a norm of zero, unless the vector contains all zeros.
  * TF-IDF may prune away most frequent words - can have 0 scalar
  * Feature scaling does affect the convergence speed of a solver: the ratio between the largest and the smallest singular values in a data matrix (called *conditional number*) is smaller. Faster convergence.
    * L2 is more sensitive to overfitting / # of iterations though.
* The *right* feature scaling can help with classification.
  * Not necessarily uniform column scaling
