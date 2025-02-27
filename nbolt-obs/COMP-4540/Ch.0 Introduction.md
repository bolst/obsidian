[[_Design and Analysis of Computer Algorithms]]

We examine algorithms under two criteria
1. Correctness
2. Efficiency

To describe an algorithm, we use whatever expressive method is clear and concise. This typically involves
- Any kind of common programming language constructs (e.g., `for loop`)
- Any mathematical expression
- English sentences if the meaning is clear
- No type declarations (should be self-explanatory)
- Hiding obvious/irrelevant details

To prove the **correctness** of an algorithm is to show that the input/output relationship specified in the problem statement is adhered by the algorithm.
This usually requires proving a sequence of lemmas/theorems about the objects that the algorithm manipulates.

## Algorithm Complexity

The **complexity** of an algorithm refers to the amount of resources it needs
1. Time complexity (main focus)
2. Space complexity (some focus)
3. Processor complexity
4. Area complexity

We do not focus on the exact time complexity but the **rate of growth** of time relative to input size.

### Example: Sequential Search

```python
Algorithm SequentialSearch(L,x)
Input: An array L of n elements; A specific element x;
Output: Index of x in L if x is in L, otherwise 0.
begin
	index := 1
	while index <= n and L[index] != x do
		index := index + 1
	if index > n then
		index := 0
end
```

Key operation: comparison of `x` with `L[i]`.
Statement 2 takes $I$ time steps, where $I\leq n$ is the number of loops.
The remaining steps take no time.
Time complexity is thus $I\leq n$ time steps.

#### Correctness of Sequential Search

We want to prove that the algorithm halts and the output is corect.

**Lemma 1:** for $1\leq k\leq n + 1$, if and when control reaches line 2 for the $k^{th}$ time, `index = k` and `L[i] != x`, $1\leq i<k$.

**Proof (induction):**
> For base case $k=1$, $index = 1 = k$ is set in line 1 and there is no $i$ such that $L[i] = x$.
> **Induction Hypothesis**: Suppose Lemma 1 holds for $k=m, m\leq n$.
> **Induction Step:** When control reaches line 2 for the $(m+1)^{th}$ time, $L[m] \neq x$ $\implies L[i]\neq x, 1\leq i < m+1$.
> 	When control reaches line 2 for the $m^{th}$ time, `index = m` (as per hypothesis).
> 	Since line 3 increases the index by 1, we have `index = m + 1` when control reaches line 2 for the $(m+1)^{th}$ time.
> So when the algorithm halts, $index = 0$ or $index\leq n$.
> 	- $index = 0 \iff index = n+1$ when control exits the while loop. By Lemma 1, $L[i]\neq x, 1\leq i < n+1 \implies$ the algorithm works correctly.
> 	- Upon exiting the while loop, $\because L[index] = x$, we have (from Lemma 1) $L[i]\neq x, 1\leq i < index \implies$ the algorithm works correctly.


## Time Complexity

Time complexity depends on (1) the size of input and (2) particular inputs.

### Worst-Case Time Complexity

The **worst-case time complexity** for an input size is taken as the maximum time complexity over all inputs of that size and is defined as

$$
T_{worst}(n) = \max{\{t(I)|I\in D_n\}}
$$
where $D_n$ is the set of all inputs of size $n$ for the problem and $t(I)$ is the number of key operations performed by the algorithm on an input $I\in D_n$.

### Average-Case Time Complexity

The **average-case time complexity** for an input size is taken as the average time complexity over all inputs of that size, and is defined as

$$
T_{ave}(n) = \sum_{I\in D_n}p(I)t(I)
$$
where $p(I)$ is the probability $I$ occurs.

### Example: Sequential Search

Note that the key operation is the comparison of $x$ with $L[i]$. The input size is $n$.

The worst-case is only when $L[n]=x$ or $x\notin L \implies T(n)=n$

For the average-case, we assume (1) the elements are distinct, (2) the probability of $x\in L$ is $q$, and (3) $x$ is equally likely to be in $L$ if it appears.
>Let $I_i$ be the case where $x=L[i], 1\leq i\leq n$ and $I_{n+1}$ be when $x\notin L$.
>$\implies p(I_i)=q/n, 1\leq i\leq n$ and $p(I_{n+1})=1-q$. Clearly $t(I_i)=i$ and $t(I_{n+1})=n$.
>$\implies T_{ave}(n)=\sum_{i=1}^{n+1}p(I_i)t(I_i)=\sum_{i=1}^{n}\left(\frac{q}{n}\right)i + (1-q)n$
>$T_{ave}(n)=(1-q/2)n+q/2$.


## Optimal Algorithms

An algorithm is **optimal** (for the worst-case) if $T(n)=\mathfrak{L}(n), \forall n$ where $\mathfrak{L}$ is a lower bound for a problem on an abstract model. Any input of size $n$ for a problem must take at least $\mathfrak{L}(n)$ time for some input of that size.

### Example: Sequential Search

**Proof (Contradiction):**
>Suppose there is an algorithm that makes $\leq n-1$ comparisons.
>Then $\exists L[k], 1\leq k \leq n$ such that $x$ is not compared with $L[k]$.
>Now consider two input lists:
>	$L[i] = L'[i]\neq x, 1 \leq i \leq n$ and $i\neq k$ and $L[k]=x$ and $L'[k]\neq x$.
>	Since the algorithm does not compare $x$ with $L[k]$ or $L'[k]$, the same output will be produced for both inputs $L$ and $L'\implies$ contradiction.


## Asymptotic Complexity

$$
\begin{align}
O(f) &\sim \text{worse case limit of f}:\\ 
&\left\{ g: \mathbb{N}\rightarrow \mathbb{R}_{\geq 0} | \exists c\in\mathbb{R}^+, n_0\in\mathbb{N}: g(n) \leq cf(n), \forall n\geq n_0 \right\} \\ \\

\Omega(f) &\sim \text{best case limit of f}:\\
&\left\{ g: \mathbb{N}\rightarrow \mathbb{R}_{\geq 0} | \exists c\in\mathbb{R}^+, n_0\in\mathbb{N}: g(n) \geq cf(n), \forall n\geq n_0 \right\} \\ \\

\Theta(f) &\sim \text{average behaviour of f}:\\ 
&\left\{ g: \mathbb{N}\rightarrow \mathbb{R}_{\geq 0} | \exists c,c'\in\mathbb{R}^+, n_0\in\mathbb{N}: cf(n) \leq g(n) \leq c'f(n), \forall n\geq n_0 \right\} \\ \\

o(f) &\sim \text{strictly-worse case limit of f}:\\
&\left\{ g: \mathbb{N}\rightarrow \mathbb{R}_{\geq 0} | \forall c\in\mathbb{R}^+, \exists n_c\in\mathbb{N}: g(n) < cf(n), \forall n\geq n_c \right\}
\end{align}
$$

### Theorem 0.1
$$
\begin{align}
&\text{(a)} \ \ g\in O(f) \iff f\in\Omega(g) \\ \\

&\text{(b)} \ \ \Theta(f) = O(f) \cap \Omega(f) \\ \\

&\text{(c)} \ \ g\in o(f) \implies g\in O(f) \text{ and } g\notin \Theta(f)
\end{align}
$$

## Proof Methods with Limits

### Theorem 0.2

$$
\begin{align}
&\text{(a)} \ \ g\in O(f) \text{ if } \lim_{n\rightarrow\infty}\frac{g(n)}{f(n)}=k\geq0 \text{ for some } k\in\mathbb{R}_{\geq 0} \\ \\

&\text{(b)} \ \ g\in \Omega(f) \text{ if } \lim_{n\rightarrow\infty}\frac{g(n)}{f(n)}=\infty \text{ or } k\gt0 \text{ for some } k\in\mathbb{R}_{\geq 0} \\ \\

&\text{(c)} \ \ g\in \Theta(f) \text{ if } \lim_{n\rightarrow\infty}\frac{g(n)}{f(n)}=k\gt0 \text{ for some } k\in\mathbb{R}^+ \\ \\

&\text{(d)} \ \ g\in o(f) \text{ if } \lim_{n\rightarrow\infty}\frac{g(n)}{f(n)}=0 \\ \\
\end{align}
$$

Each of $O,\Omega,\Theta,o$ can be regarded as a [[Binary Relations|binary relation]]. More precisely,
$$
\begin{align}
g \preceq f &\iff g\in O(f) \\ \\
g \succeq f &\iff g\in \Omega(f) \\ \\
g \asymp f &\iff g\in \Theta(f) \\ \\
g \prec f &\iff g\in o(f)
\end{align}
$$

## Theorem 0.3

$$
\begin{align}
&\text{(a)} \ \ f\in O(f), \forall f \\ \\
&\text{(b)} \ \ g\in O(f) \wedge f\in O(g) \implies g\in \Theta(f) \\ \\
&\text{(c)} \ \ g\in O(f) \wedge f\in O(h) \implies g\in O(h) \\ \\
&\text{(d)} \ \ f\in\Omega(f), \forall f \\ \\
&\text{(e)} \ \ g\in\Omega(f) \wedge f\in\Omega(g) \implies g\in\Theta(f) \\ \\
&\text{(f)} \ \ g\in\Omega(f) \wedge f\in\Omega(h) \implies g\in\Omega(h) \\ \\
\end{align}
$$

So in a way, $O,\Omega$ are [[Partial Orders|partial orders]].


## Theorem 0.4

$$
\begin{align}
&\text{(a)} \ \ \sim\left( f\in o(g) \wedge g\in o(f) \right) \\ \\
&\text{(b)} \ \ g\in o(f)\wedge f\in o(h) \implies g\in o(h) \\ \\
\end{align}
$$
So in a way, $o$ is a [[Strict Order|strict order]].


## Theorem 0.5

$$
\begin{align}
&\text{(a)} \ \ f\in\Theta(f), \forall f \\ \\
&\text{(b)} \ \ g\in\Theta(f) \implies f\in\Theta(g) \\ \\
&\text{(c)} \ \ g\in\Theta(f) \wedge f\in\Theta(h) \implies g\in\Theta(h) \\ \\
\end{align}
$$
So in a way, $\Theta$ is an [[Equivalence Relation||equivalence relation]].

