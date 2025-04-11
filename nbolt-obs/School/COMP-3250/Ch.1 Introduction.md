
Machine Learning focuses on extracting knowledge from data. It is a research field at the intersection of statistics, artificial intelligence, and computer science.

We have two types of ML:

> **[[Ch.2 Supervised Learning]]** learns from input/output pairs - it is called "supervised" because a "teacher" provides supervision to the algorithms in the form of the desired outputs.
> For example, identifying the zip code from handwritten digits on an envelope is a form of supervised ML. The input is a scan of the handwriting, and the output is the desired zip code digits. To create a dataset for building a machine learning model, you would collect many envelopes. You can read each digit yourself and store the desired outputs.

>**Unsupervised** ML is where only the input is known, and there is no known output data to give to the algorithm. While there are many successful applications, these are usually harder to understand/evaluate.
>For example, identifying topics in a set of blog posts would be a form of unsupervised ML.

One of the most important parts of ML is understanding the data you are working with and how it relates to the task you want to solve.

It is good to keep the following questions in mind:

- What question(s) am I trying to answer? Can the collected data answer that question?
- What is the best way to phrase my question(s) as a ML problem?
- Have I collected enough data to represent the problem I am trying to solve?
- What features of the data did I extract and will these enable the right predictions?
- How will I measure success in my application?
- How will the ML solution interact with other parts of my research/business product.