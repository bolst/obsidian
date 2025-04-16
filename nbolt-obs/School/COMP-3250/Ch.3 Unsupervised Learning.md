[[_Data Analytics I]]


Unsupervised learning is the set of ML algorithms with no known output.

# Types of unsupervised learning

1. **Unsupervised transformations**. This comprises of algorithms that create new representations of the data, which might be easier for humans or ML algorithms to understand compared to the original (e.g., dimensionality reduction, etc).
2. **Clustering algorithms**. This type of algorithm partitions data into distinct groups of similar items.


# Preprocessing and scaling

A common practice is to adjust features so that data representation is more suitable for the algorithms we will use.

It is important to apply exactly the same transformation to both the training and test set for the model to work on the test set.

## The StandardScaler

Scikit-learn's `StandardScaler` ensures that for each feature, the mean is 0 and variance is 1, bringing all features to the same magnitude.
- This does NOT force bounds on the dataset (such as `MinMaxScaler`)


## The RobustScaler

Similar to `StandardScaler` in the fact that it ensures statistical properties for each feature are the same. However, `RobustScaler` uses median and quartiles instead of mean and variance.
- This means that `RobustScaler` ignores outliers


## The MinMaxScaler

This shifts the data so that all features are exactly between 0 and 1.


## The Normalizer

This is a different kind of rescaling - it scales the data so that the feature vector has a Euclidean length of 1.


# Dimensionality reduction

## Principal component analysis (PCA)

PCA transforms the dataset in a way such that the transformed features are statistically uncorrelated. This transformation is often followed by selecting a subset of the new features based on how important they are.

The downside to PCA is that the "components" it outputs are often not easy to interpret. They correspond to directions of the original data, so they are combinations of the original features.
