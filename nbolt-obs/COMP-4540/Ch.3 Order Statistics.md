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
Algorithm SecondLargest1
Input: A set L of n elements from a totally ordered set
Output: SecondLargest (the second-largest element in L)
begin
	1. m := max of L
	2. SecondLargest := max of (L - {m})
end
```

**Correctness:** The second-largest element of $L$ is the largest of $(L-\{m\})$

**Time Complexity:** In [[Ch.1 Searching an Ordered List]], we have shown that finding the *max* of a list of $n$ elements can be done in $(n-1)$ comparisons.

We thus have
1. Step 1 has $(n-1)$ comparisons
2. Step 2 has $(n-2)$ comparisons
In total, $(n-1)+(n-2)=2n-3$ comparisons.


```python
Algorithm SecondLargest2
Input: A set L of n elements from a totally ordered set
Output: SecondLargest (the second-largest element in L)
begin
	while |L| > 1 do
		form floor( |L|/2 ) pairs of elements of L and compare every pair
		remove the losers of the comparisons from L
	m := the only element left in L
	form the set S = { x | x loses only to m }
	SecondLargest := max element in S
end.
```


**Correctness:**

> [!lemma|3.1]
> The second-largest element is one of the elements losing only to max in the course of determining max

^b51a49

`\begin{proof}`
Let $x \in L$ be such that $\exists y \in L, x < y, y \neq max$. This implies that $y<max$, so by transitivity $x<max$. Hence, $x$ is at best the third largest element.
`\end{proof}`

> [!theorem|3.2]
> Algorithm SecondLargest2 correctly determines the second-largest element in $L$.

`\begin{proof}`
At the end of the `while` loop, an element $x \not\in L$ iff $x$ is the loser of a comparison iff $x \neq max$. Therefore, $max$ must be in $L$.

Since on exiting the `while` loop, $L$ only contains one element, that element must be $max$.

Consequentially, the set $S$ consists of all the elements losing only to $max$. By [[#^b51a49]], the $max$ of $S$ must be the second-largest element in $L$.
`\end{proof}`


# 3.2 Lower Bound

## The first lower bound

Any algorithm that finds the second largest element can also find the largest element ($max$).

This is because the second largest element must have lost exactly once, and the winner of the comparison of which it is the loser is $max$.

Since $n-1$ is a lower bound for finding $max$, it must also be a lower bound for finding the second-largest element.


## A better lower bound

A better lower bound will be shown to be $n + \lceil \lg n \rceil - 2$ comparisons.

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

> [!remark|*]
> If an element loses a total of $m$ times, it contributes 1 unit to each of the $t_{j}, 1 \leq j \leq m$

Since any algorithm that finds the second largest element also finds the largest (see the proof of $(n-1)$ lower bound), every element except the largest, *max*, must lose at least once. As a result, $t_1=n-1$.

Let $k$ be the number of elements compared to *max* in the algorithm. By [[#Lemma 3.1]], the second largest element is the largest of the $k$ elements. It follows that to determine the second largest element, $(k-1)$ of the elements must lose a second time. As a result, $t_2 \geq k-1$.

Hence, algorithm $\mathcal A$ makes $T(n) \geq t_1 + t_2 \geq (n-1) + (k-1) = n+k-2$ comparisons.

The adversary's goal is to maximize $k$.

Let's assign a weight $w(x)$ to each element $x$ in the list $L$ where $w(x)$ is the number of elements that $x$ has been compared with and has not lost to. Initially, $\forall x\in L, w(x)=1$ .

Suppose $x$ is compared to $y$ and $x\succ y$. As $\succ$ is transitive, $x$ has now been compared to $w(x)+w(y)$ elements and has not lost to any of them. So it makes sense to let $w(x):=w(x)+w(y)$.

Once an element $y$ is defeated, we know it must not be *max*. So we can reduce its weight to $w(y):=0$. This way we are ensuring that $\sum_{x\in L}w(x)=n$ at all times.

Moreover, when the algorithm halts, its answer is correct iff $w(max)=n; w(x)=0, \forall x\neq max$.

Since $w(max)$ increases its value every time *max* wins a comparison, to maximize the value of $k$ is the same as to make $w(max)$ grow as slow as possible.

Consider any two elements $x,y$ where $w(x)>0$ and $w(y)>0$ (i.e., both $x,y$ have not lost a comparison so far). Suppose $w(x)\geq w(y)$.
$$
\begin{align}
x > y &\implies w(x) := w(x) + w(y) \leq 2w(x) \\
y > x &\implies w(y) := w(x) + w(y) \geq 2w(y)
\end{align}
$$
Therefore if the adversary sticks to the policy of *always declaring the element with the larger weight the winner*, then the weights of the elements can at most be doubled after each comparison.

Obviously, if $x,y$ are such that $w(x) > w(y)$ but $y>x$, then the adversary must increase the value of $x$ to one that is larger than $y$ so its reply would not be contradicting. 

Hence the adversary updates $w(x), \forall x \in L$ as follows.

| Case          | Adversary's reply                | Updating the weights                                          |
| ------------- | -------------------------------- | ------------------------------------------------------------- |
| $w(x) > w(y)$ | $x > y$                          | $\begin{align} w(x) &:= w(x) + w(y) \\ w(y) &:= 0\end{align}$ |
| $w(x)=w(y)>0$ | $x>y$                            | $\begin{align} w(x) &:= w(x) + w(y) \\ w(y) &:= 0\end{align}$ |
| $w(y)>w(x)$   | $y>x$                            | $\begin{align} w(y) &:= w(x) + w(y) \\ w(x) &:= 0\end{align}$ |
| $w(x)=w(y)=0$ | Consistent with previous replies | No change                                                     |

Now let $w_{i}$ denote the value of $w(max)$ after the $i$th comparison involving $max$. As previously discussed, $w_{i} \leq 2w_{i-1}$ and $w_{0}=1$. 

Therefore, $w_{i} \leq 2^i$.

Since $max$ is compared $k$ times, we have

$$
\begin{align}
&n = w_{k} \leq 2^k \\
&\implies k \geq \lceil \lg n \rceil  \\
&\implies n + \lceil \lg n \rceil - 2 \ \ \text{comparisons}
\end{align}
$$



# 3.3 Finding min and max

## Algorithm MaxMin2

```python
Algorithm MaxMin2
Input: A set L of n elements from a totally ordered set
Output: max and min of L
begin
	Form floor( n/2 ) pairs of elements of L and compare every pair
	W := {x | x won the comparison in step 1}
	D := {x | x lost the comparison in step 1}
	if n is odd then
		z := only element left out in step 1
		x := any element in W
		if z > x then
			W := (W + {z}) - {x}
			D := D + {x}
		else
			D := D + {z}
	max := maximum of W
	min := minimum of D
end.
```

### Correctness

Since $max$ never loses in any comparison and $min$ never wins in any comparison, after step 1 $max \not\in D$ and $min \not\in W$ (i.e., $max \in W$ and $min \in D$).

Since $W \subseteq L$ and $max \geq x, \forall x \in L$, therefore $max \geq x, \forall x \in W$. In other words, $max$ is the largest element of $W$. Similarly, $min$ is the smallest element of $D$.


### Time complexity

Let $|L|=n$. Steps 1 and 2 take $\begin{cases} \left\lfloor  \frac{n}{2}  \right\rfloor +1  & n \text{ odd} \\ \frac{n}{2} & n\text{ even} \end{cases} = \left\lceil  \frac{n}{2}  \right\rceil$ comparisons.

Step 3 takes $(|W| - 1) + (|D| - 1) = |W| + |D| - 2 = n - 2$ comparisons.

Therefore in total we have $\left\lceil  \frac{n}{2}  \right\rceil + n-2 = \left\lceil  \frac{3}{2}n  \right\rceil - 2$ comparisons.


### Lower bound

First observe that to find $max$, every element other than $max$ has to lose once. Similarly, to find $min$, every element other than $min$ has to win once.

Define 1 unit of info as an element that has either won the first time, or lost the first time.

To find $max$, at least $n-1$ units of info are required, and to find $min$ at least $n-1$ units of into are required.

Therefore at least $(n-1)+(n-1)=2n-2$ units of info are required to find $max$ and $min$.

> [!remark|*]
> The strategy of the adversary is to make any algorithm solving the max-and-min problem perform as many comparisons as possible in order to gain $2n-2$ units of info.

We can classify the elements into four types
1. $N$: has not yet participated in any comparison
2. $W$: has won at least once and has never lost
3. $L$ has lost at least once and has never won
4. $WL$: has won and lost at least once

This gives rise to a total of ten different types of comparisons. The highlighted regions denote what the adversary would choose based off its strategy of making the algorithm gain as few units of info as possible after each comparison.

| Element types before comparison $(x,y)$ | Outcome of comparison  | Element types after comparison $(x,y)$ | Units of info gained |
| --------------------------------------- | ---------------------- | -------------------------------------- | -------------------- |
| $N,N$                                   | ==$x>y$==<br>==$x<y$== | ==$W,L$<br>$L,W$==                     | ==2==                |
| $N,W$                                   | $x>y$<br>==$x<y$==     | $W,WL$<br>==$L,W$==                    | 2<br>==1==           |
| $N,L$                                   | ==$x>y$==<br>$x<y$     | ==$W,L$==<br>$L,WL$                    | ==1==<br>2           |
| $N,WL$                                  | ==$x>y$<br>$x<y$==     | ==$W,WL$<br>$L,WL$==                   | ==1==                |
| $W,W$                                   | ==$x>y$<br>$x<y$==     | ==$W,WL$<br>$WL,W$==                   | ==1==                |
| $W,L$                                   | ==$x>y$==<br>$x<y$     | ==$W,L$==<br>$WL,WL$                   | ==0==<br>2           |
| $W,WL$                                  | ==$x>y$==<br>$x<y$     | ==$W,WL$==<br>$WL,WL$                  | ==0==<br>1           |
| $L,L$                                   | ==$x>y$<br>$x<y$==     | ==$WL,L$<br>$L,WL$==                   | ==1==                |
| $L,WL$                                  | $x>y$<br>==$x<y$==     | $WL,WL$<br>==$L,WL$==                  | 1<br>==0==           |
| $WL,WL$                                 | $x>y$<br>$x<y$         | ==$WL,WL$<br>$WL,WL$==                 | ==0==                |

Note that in some cases, the responses of the adversary may require it to increase/decrease the values of some of the elements in order to avoid inconsistency.

> [!example|*]
> In the $N,W$ case, while the adversary's response is $x<y$, the actual values of $x$ and $y$ may be such that $x>y$, contradicting the response. To avoid such an inconsistency, the adversary would have to increase $y$'s value to one which is greater than that of $x$.

A careful examination reveals that whenever the adversary changes the value of an element, it is either increasing the value of a $W$ element or decreasing the value of an $L$ element. Since $<$ and $>$ are transitive, this will not create inconsistency between the current/previous responses.

Now, only the $N,N$ comparisons can gain 2 units of info. But there are only $n$ elements of type $N$, and once a type $N$ element changes its type it cannot go back to type $N$ again.

Therefore, at most $\left\lfloor  \frac{n}{2}  \right\rfloor$ comparisons of this type can be performed, gaining at most $n$ ($n-1$ when $n$ is odd) units of info. The remaining $\begin{cases} 2n-2-n \\ 2n-2-(n-1) \end{cases} = \begin{cases} n-2 & n\text{ even} \\ n-1 & n\text{ odd} \end{cases}$  units of info can only be gained by performing the remaining types of comparisons.

In conclusion, the total number comparisons performed is at least $\left\lceil  \frac{3}{2}n  \right\rceil-2$.


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


## Algorithm Select

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
   
3. Define A = {x ϵ sublists with medians <  m0 | x >  m0}
		  B = {x ϵ sublists with medians >= m0 | x >= m0}
		  C = {x ϵ sublists with medians <= m0 | x <= m0}
		  D = {x ϵ sublists with medians >  m0 | x <  m0}
   
   Clearly, for every x in C, x <= m0 and for every y in B, m0 <= y.
   Partition L into:
	   L0 = {x | x in C,A,D and x < m0}
	   L1 = {x in L and x = m0}
	   L2 = {x | x in B,A,D and x > m0}

4. if |L0| < k <= |L0| + |L1| then 
	   return m0
   else if k <= |L0| then
	   find the kth smallest element in L1 recursively
   else
	   find the (k - |L0| + |L1|)th smallest element in L2 recursively

```



### Correctness

By induction on $|L|$.


### Time Complexity

Let $T(n)$ be the time required by the algorithm on inputs of size $n$.

Step 1: Finding the median of 5 elements can be done in 6 comparisons, so this step takes $6\left\lceil  \frac{n}{5}  \right\rceil$ comparisons.

Step 2: Let $M$ be the set containing all the medians. The recursive call $\text{Select(M, } \left\lceil  \frac{\left\lceil  \frac{n}{5}  \right\rceil}{2} \right\rceil \text{)}$ for finding $m_{0}$ takes $T\left( \left\lceil  \frac{n}{5}  \right\rceil \right)$ comparisons.

Step 3: Use a method similar to [[Ch.2 Sorting#Algorithm Quicksort|Procedure Partition of Quicksort]] to form $L_{<}, L_{=}, L_{>}$. This takes $n-1$ comparisons.

Step 4: $\max\{ T(|L_{<}|), T(|L_{>}|) \}$ comparisons. Since $m_{0}$ is *less than or equal* to half of the $\left\lceil  \frac{n}{5}  \right\rceil$ medians found in Step 1, and each of these medians contributes 3 elements that are *greater than or equal to $m_{0}$* (except the last one which contributes at least one element), we have
$$
\begin{align}
&|B| \geq 3\left\lceil  \frac{1}{2}\left\lceil  \frac{n}{5}  \right\rceil   \right\rceil -3 \geq \frac{3n}{10}-3 \\
&\implies |L_{<}| \leq n - |B| \leq n - \left( \frac{3n}{10} - 3 \right)=\frac{7n}{10} + 3 \\
&\implies |L_{>}| \leq \frac{7n}{10} + 3
\end{align}
$$
Therefore, $\max\{ T(|L_{<}|), T(|L_{>}|) \} \leq T\left( \frac{7n}{10} + 3 \right)$.

Adding each step, we get
$$
\boxed{T(n) \leq 6\left\lceil  \frac{n}{5}  \right\rceil +T\left( \left\lceil  \frac{n}{5}  \right\rceil  \right) + (n-1) + T\left( \frac{7n}{10} + 3 \right)}
$$
To solve the recurrence, we use the guessing method (guess $T(n)=O(n)$) and can deduce that $T(n)=O(n)$.


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
