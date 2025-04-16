[[_Data Analytics I]]

We saw before how it can be helpful to augment data with additional features, like adding interactions of features or more general polynomials.

Representing your data best for a particular application is known as **feature engineering** and is one of the main tasks of data scientists trying to solve real-world problems.

# Categorical variables

Consider a dataset of adult incomes in the US, where we want to predict if someone has income of over or under $50000. The task is phrased as a classification task with two classes being $income \leq 50000$ and $income > 50000$.

Let's say for a feature "WorkClass" we have possible values of
- Government employee
- private employee
- self employed
- self employed incorporated

To encode these four values, we create four new features (each of the possible values) and assign 1's to the correspondences of their WorkClass feature (e.g., a 1 in "GovernmentEmployee" feature means that person was a government employee).

This type of encoding is called **One-Hot** or **One-out-of-N encoding**.

