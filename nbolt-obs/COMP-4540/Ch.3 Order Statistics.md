[[_Design and Analysis of Computer Algorithms]]

# Problem definition

Given a list of $n$ elements from a totally ordered set, find the $k^\text{th}$ ($1\leq k\leq n$) smallest element in the list.

Key Operation: Comparison of two items.

In [[Ch.0 Introduction]], we showed that finding the *maximum* of a list of $n$ elements can be done in $\Theta(n)$ time and space. This is similar for finding a *minimum*.

A question that this chapter will answer is: Can we do better than the $\Theta(n\log n)$ bound for any $1<k<n$?.

1. Without a loss of generality, assume all elements are distinct
2. $x\succ y \implies$ $x$ is the ***winner*** and $y$ is the ***loser***.

# 3.1 Finding the second largest element

```python
Algorithm Second-Largest-1
Input: A set L of n elements from a totally ordered set
Output: Second-largest (the second-largest element in L)
begin
	1. m := max of L
	2. Second-largest := max of (L - {m})
end
```

**Correctness:** The second-largest element of $L$ is the largest of $(L-\{m\})$

**Time Complexity:** In [[Ch.1 Searching an Ordered List]], we have shown that finding the *max* of a list of $n$ elements can be done in $(n-1)$ comparisons.

We thus have
1. Step 1 has $(n-1)$ comparisons
2. Step 2 has $(n-2)$ comparisons
In total, $(n-1)+(n-2)=2n-3$ comparisons.


# 3.2 Lower Bound

### Adversary

Consider playing a guessing game with a friend. You pick a day/month and your friend guesses the date by asking you yes/no questions. You win if they cannot guess after 8 questions.

What is the strategy? __Don't fix a date until you have no choice__. You start with a pool of all the possible answers, and answer yes/no based on whichever leaves you with the most possible answers afterwards.

E.g., "Is the month in the winter?" leaves 3 possible months if yes but 9 if no. So choose no.

> In proving a lower bound for a problem using an **adversary**, the adversary begins with the pool consisting of all possible input data.
> Given any algorithm to solve that problem, whenever a key operation is made in the algorithm, the adversary will force the outcome of the operation to be one that would allow it to discard the least number of elements from the pool.
> In a sense, the adversary forces any algorithm solving that problem to do as much work as possible. If it could force *any* algorithm to perform at least $f(n)$ key operations, then $f(n)$ is a lower bound for the given problem.

### An adversary argument

Let $\mathcal{A}$ be any algorithm for finding the second largest element. Let $t_j$ be the number of elements which loses $j$ or more comparisons in executing $\mathcal A$. Then the total number of comparisons performed by $\mathcal A$ is
$$
T(n) = t_1 + t_2 + t_3 + \cdots
$$
**Remark:** if an element loses a total of $m$ times, it contributes 1 unit to each of the $t_j$ $1 \leq j \leq m$.

Since any algorithm that finds the second largest element also finds the largest (see the proof of $(n-1)$ lower bound), every element except the largest, *max*, must lose at least once. As a result, $t_1=n-1$.

Let $k$ be the number of elements compared to *max* in the algorithm. By [[#Lemma 3.1]], the second largest element is the largest of the $k$ elements. It follows that to determine the second largest element, $(k-1)$ of the elements must lose a second time. As a result, $t_2 \geq k-1$.

Hence, algorithm $\mathcal A$ makes $T(n) \geq t_1 + t_2 \geq (n-1) + (k-1) = n+k-2$ comparisons.

The adversary's goal is to maximize $k$.

Let's assign a weight $w(x)$ to each element $x$ in the list $L$ where $w(x)$ is the number of elements that $x$ has been compared with and has not lost to. Initially, $\forall x\in L, w(x)=1$ .

Suppose $x$ is compared to $y$ and $x\succ y$. As $\succ$ is transitive, $x$ has now been compared to $w(x)+w(y)$ elements and has not lost to any of them. So it makes sense to let $w(x):=w(x)+w(y)$.

Once an element $y$ is defeated, we know it must not be *max*. So we can reduce its weight to $w(y):=0$. This way we are ensuring that $\sum_{x\in L}w(x)=n$ at all times.

Moreover, when the algorithm halts, its answer is correct iff $w(max)=n; w(x)=0, \forall x\neq max$.

Since $w(max)$ increases its value every time *max* wins a comparison, to maximize the value of $k$ is the same as to make $w(max)$ grow as slow as possible.


# 3.4 Finding the kth Smallest

We have shown that finding the min, max, and second-largest of a list of $n$ elements can all be done in $\Theta(n)$ time and space. These are just special cases of "finding the kth-smallest" problem.

**Q: Can we achieve linear time and space bounds for any k, $1<k<n$?**

One possible approach is:
1. Partition $L$ at its median $m$ (the median is the $\lceil\frac{n}{2}\rceil$th smallest element of $L$)
2. If $k=\lceil\frac{n}{2}\rceil$
	   then $m$ is the $k$th smallest element
	   else if $k < \lceil\frac{n}{2}\rceil$
		   then find the $k$th smallest element in $L[1 ... \lceil\frac{n}{2}\rceil]$ recursively
	   else find the $(k-\lceil\frac{n}{2}\rceil)$th smallest element in $L[\lceil\frac{n}{2}\rceil+1 ... n]$ recursively

Since we eliminate half the list in each iteration from further consideration, if we can complete the portion in $O(n)$ time, then the entire algorithm will take $O(n)$ time.
i.e.,

$$
n + \sum\frac{n}{2^i} < 2n
$$
However, partitioning $L$ at its median requires knowing the median. Intuitively, finding the median is the most difficult case in finding the $k$th smallest element. So this approach is not feasible.

### Algorithm Select

Instead of getting rid of half of the list in each iteration, we can get rid of a fixed fraction of the list (i.e., $n/c$ for some constant $c$).

The following $O(n)$ algorithm is based on this idea.

```python
Algorithm Select(L,k)
Input: L[1...n], k (1 <= k <= n)
Output: kth smallest element in L

1. Divide L into ceil(n/5) sublists such that the first ceil(n/5)-1 sublists contain exactly 5 elements each, while the remaining one will contain n%5 elements. Find the median of each sublist.
   
   e.g.,
   {1,2,3,4,5,6,7,8,...,98}
   -> {1,2,3,4,5}, {6,7,8,9,10}, ..., {96,97,98}
   -> medians = {3, 8, 13, ..., 97}
   
2. Use Algorithm Select recursively to find the median of the ceil(n/5) medians obtained in Step 1. Let it be m0.
   
   e.g.,
   {3, 8, 13, ..., 97} -> m0 = 53
   
3. Define A = {x ϵ sublists with medians <  m0 | x <  m0}
		  B = {x ϵ sublists with medians >= m0 | x >= m0}
		  C = {x ϵ sublists with medians <= m0 | x >= m0}
		  D = {x ϵ sublists with medians >  m0 | x <  m0}
   
   Clearly, for every x in C, x <= m0 and for every y in B, m0 <= y.
   Partition L into:
	   L0 = {x | x in C,A,D and x < m0}
	   L1 = {x in L and x = m0}
	   L2 = {x | x in B,A,D and x > m0}

4. If |L0| < k <= |L0| + |L1| then return m0
   Else if k <= |L0| then
	   find the kth smallest element in L1 recursively
   Else
	   find the (k - |L0| + |L1|)th smallest element in L2 recursively

```


# 3.5 Reduction


Reduction is a technique that transforms one problem into another so that a solution to the latter provides a solution to the former. It is frequently used in relating the difficulty of one problem to another and for deriving lower bounds.

### Definition: Problem instance

Let $\Pi$ be a computational problem. A **problem instance** of $\Pi$ is an input to any algorithm that solves $\Pi$.


### Definition: Reducible

Let $\Pi_1, \Pi_2$ be two computational problems. We say $\Pi_1$ is **reducible** to $\Pi_2$ if
- there exists an algorithm $A_\pi$ that transforms any problem instance $\pi_1$ of $\Pi_1$ to a problem instance $\pi_2$ of $\Pi_2$
- there exists an algorithm $A_S$ that transforms any solution $s_2$ of $\Pi_2$ to a solution $s_2$ of $\Pi_1$.

>For example, consider the problem of multiplying two numbers.
>Multiplication is reducible to addition, since we can apply the algorithm for logarithms ($A_\pi$) on both numbers, add the results, and then apply the algorithm for inverse logarithms ($A_S$) to get the multiplied answer.


### Lemma 3.6

>[!lemma|3.6] 
>
>Let $\Pi_1,\Pi_2$ be two computational problems such that $\Pi_1$ is $\tau(n)$-transformable to $\Pi_2$. If problem $\Pi_2$ can be solved in $O(T_2(n))$ time, then problem $\Pi_1$ can be solved in $O(T_2(n) + \tau(n))$ time

^ee6581

`\begin{proof}`
The following algorithm solves problem $\Pi_1$:
1. Given a problem instance $\pi_1$ of $\Pi_1$, transform $\pi_1$ into a problem instance $\pi_2$ of $\Pi_2$.
2. Present $\pi_2$ as an input of $O(T_2(n))$-time algorithm that solves $\Pi_2$. The algorithm outputs the solution $S_2$.
3. Transform $S_2$ to $S_1$, a solution for $\Pi_1$ on input $\pi_1$.

Steps 1,3 take $O(\tau(n))$ time. Step 2 takes $O(T_2(n))$ time. The algorithm thus takes $O(T_2(n))+O(\tau(n))=O(T_2(n)+\tau(n))$ time.
`\end{proof}`

### Theorem 3.7 (lower-bound)

> [!theorem|3.7] Lower bound
> 
> Let $\Pi_1,\Pi_2$ be two computational problems such that $\Pi_1$ is $\tau(n)$-transformable to $\Pi_2$. If it is known that $\Omega(T_1(n))$ is a lower bound on time complexity for solving problem $\Pi_1$ such that $\tau(n)\ \in o(T_1(n))$. Then $\Omega(T_1(n))$ is a lower bound on time complexity for solving problem $\Pi_2$.

`\begin{proof}`
By [[#^ee6581]], there exists an algorithm that solves problem $\Pi_1$ in $o(T_1(n))+O(\tau(n))=o(T_1(n))$ time. This contradicts the assumption that $\Omega(T_1(n))$ is a lower bound for $\Pi_1$.
`\end{proof}`
