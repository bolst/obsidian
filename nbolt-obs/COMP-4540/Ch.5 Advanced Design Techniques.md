[[_Design and Analysis of Computer Algorithms]]

# 5.1 Dynamic Programming

Programming in this context refers to the use of a [[Tabulation method|tabulation method]], not to writing computer programs.

## The divide-and-conquer technique

1. Divide a given problem into smaller instances of the same problem
2. Solve the smaller problems and then combine their solutions to solve the given problem

When the smaller problems are *not independent*, the divide-and-conquer technique may become inefficient since it will do more work than necessary by having to solve the same subproblems repeatedly (e.g., Fibonacci numbers).

**Dynamic Programming** solves every smaller problem just once, and then saves the result in a table. The solution will be retrieved when the same subproblem is encountered later on, which avoids the redundant work of recomputing the same solution.

Dynamic programming is typically applied to [[Optimization problems|optimization problems]].


## 5.1.1 Matrix-chain multiplication

Suppose we have two matrices $A \in \mathcal{M}_{{p,q}}$ and $B \in \mathcal{M}_{q,r}$.

The well-known *standard algorithm* for multiplying a pair of matrices takes $pqr$ scalar multiplications to compute $A \times B$.

A matrix product of a chain of matrices is **fully parenthesized** if it is
1. A single matrix, or
2. The product of two fully parenthesized products enclosed within a pair of parenthesis

> [!definition|*] Problem
> Given a chain $A_1, A_2, \cdots, A_n$ of $n$ matrices, in which matrix $A_i$ has dimensions $d_{i-1} \times d_i$, obtain an *optimal parenthesization* for the product of the chain which is a fully parenthesized product that requires the minimum number of scalar multiplications.

### A brute-force method

We exhaustively check all different fully parenthesized products and pick one that requires the minimum number of scalar multiplications.

Let $\mathcal{P}(n)$ denote the total number of fully parenthesized products of the chain of matrices $A_1, \cdots, A_n$. There are $(n-1)$ ways of splitting the chain into two subchains, namely

$$
A_{1}, A_{2}, \cdots, A_{i} \text{ and } A_{i+1}, A_{i+2}, \cdots, A_{n}, \ 1 \leq i < n
$$

Subchain $A_1,A_2,\cdots,A_i$ has $\mathcal{P}(i)$ different fully parenthesized products, where subchain $A_{i+1}, A_{i+2}, \cdots, A_{n}$ has $\mathcal{P}(n-i)$ different fully parenthesized products. So we have the recurrence

$$
\mathcal{P}(n) = \begin{cases}
1 & \text{if } n=1 \\
\sum_{i=1}^{n-1} \mathcal{P}(i) \mathcal{P}(n-i) & \text{if } n>1
\end{cases}
$$

Solving this would show that the total number of fully parenthesized products of a chain of $n$ matrices is *exponential* in $n$, implying that the brute-force method has an exponential time complexity.

### Observation

1. The last multiplication in any optimal parenthesization of the matrix-chain $A_1, \cdots, A_n$ must involve the products of two subchains in the form
   $$
A_{1}, A_{2}, \dots, A_{k} \ \text{and} \ A_{k+1}, A_{k+2}, \dots, A_{n}
   $$
2. The fully parenthesized product of each of the subchains (within this optimal parenthesization of the given chain) must be an optimal parenthesization itself
3. Observation 2 can be generalized to $M(i,j), 1 \leq j < j \leq n$.

Suppose the optimal parenthesization of matrix chain $A_i, A_{i+1}, \dots, A_{j}$ splits the chain between $A_k$ and $A_{k+1}$. Then the fully parenthesized product of each $A_{i},A_{i+1},\dots,A_{k}$ and $A_{k+1}, A_{k+2}, \dots, A_{j}$ is an optimal parenthesization. 

In other words, let $M(l,u)$ denote the number of scalar multiplications required by the optimal parenthesization of $A_{l}, A_{l+1}, \dots, A_{u}$. Then

$$
M(i,j) = M(i,k) + M(k+1,j) + d_{i-1}d_{k}d_{j}
$$

We can construct the optimal parenthesization of a matrix chain based on the optimal parenthesization of its subchains. The algorithm below uses this idea.

Let $M(i,j)$ be the number of scalar multiplications required by the optimal parenthesization of the matrix chain $A_{i}, A_{i+1}, \dots, A_{j}$. 

When $i=j$, no scalar multiplication is performed so $M(i,j)=0$.

When $i<j$, let the optimal parenthesization of the matrix chain split the chain between $A_k$ and $A_{k+1}$, where $i \leq k < j$. If we know $k$, then by Observation 3

$$
M(i,j) = M(i,k) + M(k+1,j) + d_{i-1}d_{k}d_{j}
$$

but we do not know $k$, so we must somehow determine it. Since $k$ can only be any value in $[i,j-1]$, there are $(j-i$) ways of splitting the chain. Of all these ways, the one that gives the smallest $M(i,j)$ is the desired value of $k$. We thus have the following recurrence

$$
M(i,j) = \begin{cases}
\min_{i\leq k < j}{\{ M(i,k) + M(k+1,j) + d_{i-1}d_{k}d_{j} \}} && i < j \\
0 && i=j
\end{cases}
$$

> [!remark|*]
> When $M(i,j), 1 \leq i < j \leq n$ is determined, the value of $k$ at which the optimal parenthesization of $A_{i}, A_{i+1}, \dots, A_{j}$ splits into two subchains is also determined. The optimal parenthesization of the given matrix chain can then be constructed recursively based on the $k$ values of its subchains.

We could easily write out a recursive algorithm to determine $M(i,j)$ based on the above recurrence relation, but the resulting algorithm will be extremely inefficient. This will be because many subproblems will have to be recomputed multiple times (this algorithm will take $O(a^n)$ time!).

We can use dynamic programming so that the algorithm computes each subproblem only once, and then stores the value in a table for later reference.

### Algorithm MatrixChain

```python
Algorithm MatrixChain
Input: 
	dimensions of matrices d[0 ... n] such that A[i] ~ (d[i-1], d[i]);
Output:
	M[i,j]
begin
	for i:=1 to n do 
		M[i,i] := 0            # base case
	for l:=2 to n do
		for i:=1 to n-l+1 do
			j := i + l - 1
			M[i,j] := ∞        # initialize M[i,j]
			for k:=i to j-1 do
				c := M[i,k] + M[k+1,j] + d[i-1]*d[k]*d[j]
				if c < M[i,j] then 
					M[i,j] := c
					factor[i,j] := k
			
end
```

> [!example|*]
> Consider $A_{1},A_{2},A_{3},A_{4}$ with respective dimensions 30x1, 1x40, 40x10, 10x25
> 
> $M[1,2] = \min \{ M[1,1] + M[2,2] + 30*1*40 \} = 1200$
> $M[2,3] = \min \{ M[2,2] + M[3,3] + 1*40*10 \} = 400$
> $\vdots$
> $M[1,3] = \min \{ M[1,1] + M[2,3] + 30*1*10, M[1,2] + M[3,3] + 30*40*10 \}$
> $= \min \{ 700, 13200 \} = 700$
> $\vdots$


#### Correctness

Using mathematical induction on $k$, it is easily verified that the innermost for loop and the initialization of $M[i,j]$ directly above it computes the expression

$$
\min_{i \leq k < j}{\{ M(i,k) + M(k+1,j) + d_{i-1}d_{k}d_{j} \}}
$$

for the correct value of $k$.

For the rest, consider the following

> [!claim|*]
> At the end of the $(l-1)$th iteration of the second for loop, $M[i,j]$ and $factor[i,j]$ are correctly computed for all the subchains of length $\leq l$.

`\begin{proof}`
**(Induction basis)** When $l=1$, "at the end of the 0th iteration" can be interpreted as "before entering the for loop".
The $M[i,j]$ values for all the subchains of length 1 (i.e., $i=j$) are indeed correctly computed in the first for loop.

**(Induction Hypothesis)** Suppose the assertion holds for iteration $h-1$.

**(Induction Step)** During the $h$th iteration (i.e., when $l=h+1$), the inner for loop computes $M[i,j]$ for

$i$ varying from $1$ to $n-l+1$ and

$j$ varying from $1+l-1=l$ to $n-l+1+l-1=n$.

In other words, $M[1,l], M[2,l+1],\dots,M[n-l+1,n]$ which include exactly all of the subchains of length $l=h+1$.

Now, as $i \leq k < j$, the length of subchains $A_{i}, A_{i+1}, \dots, A_{k}$ and $A_{k+1},A_{k+2}, \dots, A_{j}$ are both $<l=h+1$.

So, by the induction hypothesis, $M[i,k], M[k+1,j], i \leq k < j$, are already correctly computed.

$M[i,j]$, and hence $factor[i,j]$ for $j-i+1=h+1$ are thus correctly computed in the innermost for loop.

Consequently, upon exiting the second for loop (i.e., when the $(n-1)$th iteration terminates), $M[1,n]$ and $factor[1,n]$ are correctly computed.
`\end{proof}`

It remains to consider how to produce the optimal parenthesized product.

#### Algorithm ShowOrder

```python
Algorithm ShowOrder(factor, i, j)
begin
	if (i = j) then 
		print('A_i')
		return
	else
		k := factor[i,j]
		print('(')
		ShowOrder(factor, i, k)
		print('x')
		ShowOrder(factor, k+1, j)
		print(')')
end

call ShowOrder(factor, 1, n)
```



## 5.1.2 Characteristics of optimization problems to which dynamic programming may apply

1. Optimal Substructure
   An optimal solution to the problem contains within its optimal solutions to subproblems.
   This would allow the same method used in solving the given problem to be applied to the subproblems
2. Overlapping Subproblems
   Subproblems generated within the optimal solution of the given problem reappears over and over again.
   This means that each subproblem needed to be solved once and the solution is stored in a table, so that whenever solution of that subproblem is needed, an $O(1)$ time table lookup can be performed to retrieve its value.


## 5.1.3 Travelling salesman problem

> [!definition|*] Problem
> Given a weighted graph $G=(V,E,c)$ where
> $$\begin{align}V&=\{ v_{1}, v_{2}, \dots, v_{n} \} \ \ \ \ \text{and} \\ c(v_{i}, v_{j}) &= \begin{cases} c_{ij} > 0 && \text{if} (v_{i}, v_{j} \in E \\ \infty && \text{if} (v_{i},v_{j}) \notin E\end{cases} \end{align}$$
> 
> Find a Hamiltonian cycle (called an [[Optimal tour|optimal tour]])
> $$H : v_{\pi(1)} v_{\pi(2)} \dots v_{\pi(n)} v_{\pi(1)}$$
> 
> such that the total weight cost is minimum
> $$cost(H) = \sum_{i=1}^{n-1} c_{\pi(i)\pi(i+1)} + c_{\pi(n)\pi(1)} $$


> [!claim|*] The Optimal substructure
> Suppose $H$ is an optimal tour. Without loss of generality, we assume the tour starts at vertex $v_1$. Let $v_k$ be the vertex following $v_1$ in $H$.
> Then $H$ consists of the edge $(v_1,v_k)$ and a path $P$ from $v_k$ to $v_{1}$ which passes through every vertex in $V-\{ v_{1}, v_{k} \}$ exactly once.
> The path $P$ must have minimum total cost among all such paths.

`\begin{proof}`
Suppose $P'$ is a path from $v_k$ to $v_{1}$ which passes through every vertex in $V-\{ v_{1},v_{k} \}$ exactly once and has a total cost $cost(P') < cost(P)$. Then the edge $(v_1, v_{k})$ and the path $P'$ would form a Hamiltonian cycle $H'$ with total cost $cost(H')=c_{1k} + cost(P') < c_{1k}+cost(P) = cost(H)$. Contradiction!
`\end{proof}`

It follows that if we let $cost(v_i, S)$ denote the total cost of a path starting at $v_{i}$, going through every vertex in $S$ exactly once and then ending at $v_{1}$, then from the optimal substructure:

$$
cost(H) = cost(v_{1}, V-\{v_{1}\}) = c_{1k}+cost(v_{k}, V-\{v_{1}, v_{k}\})
$$

However, we don't know the value of $k$ so we must determine $k$. As a result:

$$
cost(H) = cost(v_{1}, V-\{v_{1}\}) = \min_{2 \leq k \leq n}{\{ c_{1k} + cost(v_{k}, V-\{v_{1}, v_{k}\}) \}}
$$

Generalizing the above recurrence to deal with the subproblems, we obtain $\forall S \subseteq V - \{ v_{1} \}$ such that $v_{i} \notin S$:

$$
\begin{cases}
cost(v_{i}, S) = \min_{v_{k} \in S}{ \{ c_{ik} + cost(v_{k}, S - \{ v_{k} \}) \}} \\
cost(v_{i}, \emptyset) = c_{i1}
\end{cases}
$$
The goal is to compute $cost(v_1, V-\{ v_1 \})$ using the recurrence above.

> [!example|*]
> 
> $V = \{ v_{1}, v_{2}, v_{3}, v_{4} \}$
> $$\begin{array} {|r|r|}\hline  & v_1 & v_2 & v_3 & v_4 \\ \hline v_1 & 0 & 10 & 15 & 20 \\ \hline v_2 & 5 & 0 & 9 & 10 \\ \hline v_3 & 6 & 13 & 0 & 12 \\ \hline v_4 & 8 & 8 & 9 & 0 \\ \hline  \end{array}$$

(i) $S = \emptyset$
$$
\begin{align}
cost(v_{2}, \emptyset) &= c_{21} = 5 \\
cost(v_{3}, \emptyset) &= c_{31} = 6 \\
cost(v_{4}, \emptyset) &= c_{41} = 8
\end{align}
$$

(ii) $|S| = 1$
$$
\begin{align}
cost(v_{2}, \{ v_{3} \}) = c_{23} + cost(v_{3}, \emptyset) &= 9 + 6 = 15 \\
cost(v_{2}, \{ v_{4} \}) = c_{24} + cost(v_{4}, \emptyset) &= 10 + 8 = 18 \\
cost(v_{3}, \{ v_{2} \}) = c_{32} + cost(v_{2}, \emptyset) &= 13 + 5 = 18 \\
cost(v_{3}, \{ v_{4} \}) = c_{34} + cost(v_{4}, \emptyset) &= 12 + 8 = 20 \\
cost(v_{4}, \{ v_{2} \}) = c_{42} + cost(v_{2}, \emptyset) &= 8 + 5 = 13 \\
cost(v_{4}, \{ v_{3} \}) = c_{43} + cost(v_{3}, \emptyset) &= 9 + 6 = 15
\end{align}
$$
(iii) $|S| = 2$
$$
\begin{align}
cost(v_{2}, \{ v_{3},v_{4} \}) &= \min \{ c_{23} + cost(v_{3}, \{ v_{4} \}), c_{24} + cost(v_{4}, \{ v_{3} \}) \} \\
&= \min \{ 9 + 20, 10 + 15 \} = 25 \\
cost(v_{3}, \{ v_{2}, v_{4} \}) &= \min \{ c_{32} + cost(v_{2}, \{ v_{4} \}), c_{34} + cost(v_{4}, \{ v_{2} \}) \} \\
&= \min \{ 13 + 18, 12 + 13 \} = 25 \\
cost(v_{4}, \{ v_{2},v_{3} \}) &= \min \{ c_{42} + cost(v_{2}, \{ v_{3} \}), c_{43} + cost(v_{3}, \{ v_{2} \}) \} \\
&= \min \{ 8+15, 9+18 \} = 23
\end{align}
$$

(iv) $|S| = 3$
$$
\begin{align}
cost(v_{1}, \{ v_{2},v_{3},v_{4} \}) = \min \{ &c_{12} + cost(v_{2}, \{ v_{3},v_{4} \}), \\
 &c_{13} + cost(v_{3}, \{ v_{2},v_{4} \}),  \\
&c_{14} + cost(v_{4}, \{ v_{2},v_{3} \}) \} \\
= \min \{ 10+25, 15+25, 20+23 \} = 35
\end{align}
$$

To construct the optimal tour, we keep, for each $cost(v_{i}, S)$, the index $k$ that minimizes the RHS of the above recurrence. Let $next(v_i, S)$ denote the index $k$ giving rise to $cost(v_{i},S)$. Then, in the above example, the optimal tour:
1. Starts at $v_1$
2. Enters $v_2$ ($\because next(v_{1}, \{ v_{2},v_{3},v_{4} \}) = 2$)
3. Then $v_4$, ($\because next(v_{2}, \{v_{3},v_{4} \}) = 4$)
4. Then $v_{3}$, ($\because next(v_{4}, \{ v_{3} \}) = 3$)
5. Finally returns to $v_{1}$


#### Time Complexity

The total time complexity is equivalent to the time required to compute $cost(v_{i}, S), \forall v_{i} \in V - \{  v_{1} \}$ and the time required to compute $cost(v_{1}, V - \{ v_{1} \})$.

To determine the time required to compute $cost(v_{i}, S), \forall v_{i} \in V - \{ v_{1} \}, \forall S \subseteq V - \{ v_{1}, v_{i} \}$, we shall first count the number of $cost(v_{i}, S)$'s with $|S| = k$ for $0 \leq k \leq n - 2$.

Since $v_{i} \in V - \{ v_{1} \}$, there are $(n-1)$ ways of selecting $v_{i}$.

For each $v_{i}$, as $v_{1}, v_{i} \not\in S$, there are $n-2 \choose k$ ways of selecting $k$ distinct elements from $V - \{ v_{1}, v_{i} \}$ to form $S$. In other words, there are $n-2 \choose k$ distinct $S$ of size $k$.

It follows that there are $(n-1) \binom{n-2}{k}$ distinct $cost(v_{i}, S)$'s with $|S| = k$.

Computing the value of each $cost(v_{i}, S)$ with $|S| = k$ takes $O(k)$ time ($k$ comparisons).

Therefore, the time required to compute all of the $cost(v_{i}, S)$'s with $|S| = k$ is $O(k(n-1)\binom{n-2}{k})$.

The time required to compute all the $cost(v_{i},S)$'s is thus

$$
\begin{align}
\sum_{k=0}^{n-2} O\left(k(n-1) \binom{n-2}{k}\right) &= O\left( \sum_{k=0}^{n-2} n(n-1)\binom{n-2}{k} \right) \\
&= O \left( n^2 \sum_{k=0}^{n-2} \binom{n-2}{k} \right) \\
&= O(n^2 2^n)
\end{align}
$$

Finally, the time required to compute $cost(v_{1}, V - \{ v_{1} \})$ is $O(n)$. The total time complexity is thus $O(2^n n^2)$.


## 5.1.4 k-approximation string matching



