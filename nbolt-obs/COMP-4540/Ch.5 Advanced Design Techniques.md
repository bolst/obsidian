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


#### Time complexity

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


# 5.2 Greedy Algorithms

A greedy algorithm for an optimization problem goes through a sequence of steps and makes a choice at each step so as to arrive at an *optimal* solution.

In each step, there are a number of choices to make and the greedy algorithm always makes a *locally optimal choice* (greedy choice) hoping that the choice will lead to a *globally optimal solution*. 


### 5.2.1 Coin changing

> [!definition|*] Problem
> Given $n$ cents, make change for the cents using the smallest number of (Canadian) coins (quarters, dimes, nickels, pennies).

Method: repeatedly select the largest-denomination coin that is not larger than the amount still owed (i.e., a greedy choice).

#### Algorithm: GreedyChange

```python
Algorithm GreedyChange
Input: n cents
Output: q quarters, d dimes, f nickels, p pennies such that 25q + 10d + 5f + p = n, and q + d + f + p is the minimum among all solutions of the equations.
begin
	q := n // 25
	n := n % 25
	
	d := d // 10
	n := d % 10
	
	f = n // 5
	
	p := n % 5
end
```

##### Correctness

> [!lemma|*]
> Algorithm GreedyChange makes a change of $n$ cents using the minimum number of coins.

`\begin{proof}`
Suppose in the optimal change, $q,d,f,p$ quarters, dimes, nickels and pennies are (respectively) used.

If $p \geq 5$, then by replacing 5 pennies with 1 nickel, we obtain a change of $n$ cents, using less number of coins (a contradiction!). Therefore $p \leq 4$.

If $f \geq 2$, then by replacing 2 nickels with 1 dime, we obtain a change of $n$ cents, using less number of coins (a contradiction!). Therefore $f \leq 1$.

If $d \geq 3$, then by replacing 3 dimes with 1 quarter and 1 nickel, we obtain a change of $n$ cents, using less number of coins (a contradiction!). Therefore $d \leq 2$.

However, if $d = 2$, then $f=0$, for otherwise, replacing 2 dimes and 1 nickel with 1 quarter gives rise to a change using less coins (a contradiction!).

Hence, either $d=2, f=0$ and $p \leq 4$, or $d \leq 1, f \leq 1$ and $p \leq 4$.
This immediately implies that in the optimal change, the coins which are not quarter could give rises to a sum of at most
$$
2 \times 10 \textcent + 0 \times 5\textcent + 4 \times 1\textcent = 24\textcent
$$
We thus have $n = q \times 25\textcent + n'$ where $n' \leq 24 \textcent$.

It follows that $q = \lfloor n / 25 \rfloor$. The remaining amount of change is $n' = n \bmod 25$.

Since $f \leq 1$ and $p \leq 4$, in the optimal change for $n'$, the coins which are not dimes can give rises to a sum of at most 
$$
1 \times 5\textcent + 4 \times 1\textcent = 9 \textcent
$$
So, $n' = d \times 10\textcent + n''$ where $n'' \leq 9 \textcent$.

It follows that $d = \lfloor n' / 10 \rfloor$. The remaining amount of change is $n'' = n' \bmod 10$.

Since $p \leq 4$, in the optimal change for $n''$, the coins which are not nickels can give rises to a sum of at most
$$
4 \times 1\textcent = 4\textcent
$$

So, $n'' = f \times 5\textcent + n'''$ where $n''' \leq 4\textcent$.

It follows that $f = \lfloor n'' / 5 \rfloor$. The remaining amount of change is $n''' = n'' \bmod 5$.
Obviously $p=n'''$. Hence, Algorithm GreedyChange makes an optimal change of $n$ cents using the minimum number of coins.
`\end{proof}`

### 5.2.2 Huffman codes


### 5.2.3 Single source shortest paths

> [!definition|*] Problem
> Given a weighted directed graph $G = (V,E,w)$ in which every edge $e \in E$ has a non-negative weight $w(e)$ and a source vertex $s$, compute the shortest path from $s$ to all other vertices in the graph.


#### Dijkstra's algorithm

$\forall u \in V$, 
$$
\begin{align}

d[u]&: \text{the current estimate for the shortest-path length from } s \text{ to } v. \\
\delta(s,u)&: \text{the length of a shortest path from } s \text{ to } u.
\end{align}
$$
Initially, $d[u] = \infty, u \in V - \{ s \}$ and $d[s] = 0$.
During execution, $d[u] \geq \delta(s,u)$. When execution terminates, $d[u] = \delta(s,u)$.

**Key idea:** 
$S (\subseteq V):$ a set of vertices whose shortest-path lengths from $s$ have been determined. Starting from $S = \emptyset$, repeatedly select a vertex $u \in V - S$ that has the smallest $d[u]$ value (the greedy choice), add $u$ to $S$, and update $d[v]$ for every $v \in V-S$ that is adjacent to $u$.

Maintain a min-priority queue $Q$ with $d[\cdot]$ as the key to keep $V-S$ so that the vertex $u$ can be retrieved rapidly.

```python
Dijkstras Algorithm
Input: A weighted directed graph G = (V,E,w) with non-negative edge weights, and a source vertex s
Output: d[v]: the shortest-path length from s to v (for all v in V).
begin
	d[s] := 0
	for each v in (V - {s}) do
		d[v] := ∞
	Q := V
	while len(Q) != 0 do
		u := extractMin(Q)
		for each (u,v) in E do
			d[v] := min{ d[v], d[u] + w(u,v) }
end
```


##### Correctness

Let $l(P)$ denote the length of path $P$.
Let $\delta(u,v)$ denote the length of a *shortest path* from $u$ to $v$.

> [!lemma|1]
> let $P: u_{1} u_{2} \dots u_{h}$ be a shortest path from $u_{1}$ to $u_{h}$. For $1 \leq i < j \leq h$, the sub-path $p_{ij}: u_{i} u_{i+1} \dots u_{j-1} u_{j}$ of $P$ is a shortest-path from $u_{i}$ to $u_{j}$.

`\begin{proof}`
Vertices $u_{i},u_{j}$ decompose $P$ into sub-paths $p_{1i}, p_{ij}$ and $p_{jh}$:
$$
u_{1} \stackrel{p_{1i}}{ \to } u_{i} \stackrel{p_{ij}}{ \to } u_{j} \stackrel{p_{jh}}{ \to } u_{h}
$$
Then $\delta(u_{1}, u_{h}) = l(p_{1i}) + l(p_{ij}) + l(p_{jh})$. Suppose $p_{ij}$ is not a shortest path from $u_{i}$ to $u_{j}$.
Let $p'_{ij}$ be a path from $u_{i}$ to $u_{j}$ such that $l(p'_{ij}) < l(p_{ij})$.

Then $u_{1} \stackrel{p_{1i}}{ \to } u_{i} \stackrel{p_{ij}}{ \to } u_{j} \stackrel{p_{jh}}{ \to } u_{h}$ is a path from $u_{1}$ to $u_{h}$ whose length is $l(p_{1i}) + l(p'_{ij}) + l(p_{jh})$.
Then
$$
\begin{align}
&l(p'_{ij}) < l(p_{ij}) \\
&\implies l(p_{1i}) + l(p'_{ij}) + l(p_{jh}) < l(P_{1i}) + l(P_{ij}) + l(P_{ji}) \\
&\implies l(p_{1i}) + l(p'_{ij}) + l(p_{jh}) < \delta(u_{1}, u_{h}) \\
&\implies P \text{ is not a shortest path from } u_{1} \text{ to } u_{h} \text{ (a contradiction!)}
\end{align}
$$
`\end{proof}`

> [!lemma|2]
> Let $v \in V$. Then $\delta(s,v) \leq \delta(s,u) + \delta(u,v), \forall u \in V - \{ v \}$.

^b0f6a4

`\begin{proof}`
(By contradiction).
Suppose $\exists u \in V - \{  v \}$ such that $\delta(s,v) > \delta(s,u) + \delta(u,v)$. Then, there is a path from $s$ to $v$ passing through $u$ whose length is shorter than that of any shortest path from $s$ to $v$ (a contradiction!).
`\end{proof}`

> [!lemma|3]
> $\forall v \in V, d[v] \geq \delta(s,v)$ during an execution of Dijkstra's algorithm.

^1bf015

`\begin{proof}`
(By induction on the number of times the instruction `d[v] := min{ d[v], d[u] + w(u,v) }` is executed).
(Basis) Initially $d[v] = \infty \implies d[v] \geq \delta(s,v), \forall v \in V$.

(Hypothesis) Suppose after the instruction is executed for $h-1$ times, $\forall v \in V, d[v] \geq \delta(s,v)$.

(Step) When the instruction is executed for the $h$th time, if $d[v] < d[u] + w(u,v)$, then $d[v] := \min \{ \dots \} \implies d[v]$ remains unchanged. By the induction hypothesis, $d[v] \geq \delta(s,v)$.

If $d[v] \geq d[u] + w(u,v) \cdots (A)$
by [[#^b0f6a4]], $\delta(s,v) \leq \delta(s,u) + \delta(u,v) \cdots (I)$.
By the induction hypothesis, $d[u] \geq \delta(s,u) \cdots (II)$.
Moreover, $w(u,v) \geq \delta(u,v)$, as the edge $(u,v)$ is a $u-v$ path $\cdots (III)$.
We thus have:
$$
\begin{align}
\delta(s,v) &\leq d[u] + \delta(u,v) & \text{ (by (I), (II))} \\
\implies \delta(s,v) &\leq d[u] + w(u,v) & \text{ (by (III))} \\
\implies \delta(s,v) &\leq d[v] & \text{ (by (A))}
\end{align}
$$

`\end{proof}`

> [!theorem|4]
> When Dijkstra's algorithm terminates execution, $d[u] = \delta(s,u), \forall u \in V$.

`\begin{proof}`
(By contradiction).
Suppose $\exists v \in V, d[v] \neq \delta(s,v)$ when execution of Dijkstra's algorithm terminates. Then, by [[#^1bf015]], $d[v] > \delta(s,v)$.

Since $d[v]$ remains unchanged after vertex $v$ was removed from $Q$, $d[v] > \delta(s,v)$ when $v$ was removed from $Q$.

Let $z$ be the first vertex such that $d[z] > \delta(s,z)$ when $z$ was to be removed from $Q$.

Let $P_{z}: (s=) u_{1}u_{2} \dots u_{h} (=z)$ be a shortest path from $s$ to $z$. Consider the iteration of the while loop where $z$ was to be removed from $Q$. 

Let $u_{k}$ be the vertex with the smallest index on $P_{z}$ that had *not* been removed from $Q$.

Then $u_{i}, 1 \leq i < k$ had been removed from $Q \implies P_{z}: (s=)u_{1}u_{2}\dots u_{k-1}u_{k} \dots u_{h} (=z)$.

(i) $k=h$.
Then $u_{h-1}$ had been removed from $Q \implies d[u_{h-1}] = \delta(s, u_{h-1})$.
During the iteration of the while loop when $u_{h-1}$ was removed from $Q$, the edge $(u_{h-1}, u_{h}) = (u_{h-1}, z)$ was processed within the for each loop, resulting in
$$
\begin{align}
&d[z] \leq d[u_{h-1}] + w(u_{h-1}, z) \\
&\implies d[z] \leq \delta(s, u_{h-1}) + w(u_{h-1}, z) \\
&\implies \delta(s, u_{h-1}) + w(u_{h-1},z) = \delta(s,z) \\
&\implies d[z] \leq \delta(s,z) & \text{ (a contradiction!)}
\end{align}
$$

(ii) $k < h$
Then $u_{k-1}$ has been removed from $Q \implies d[u_{k-1}] = \delta(s, u_{k-1})$.
During the iteration of the while loop where $u_{k-1}$ was removed from $Q$, the edge $(u_{k-1},u_{k})$ was processed within the for each loop, resulting in
$$
\begin{align}
d[u_{k}] &\leq d[u_{k-1}] + w(u_{k-1}, u_{k}) \\
&= \delta(s,u_{k-1}) + w(u_{k-1}, u_{k})
\end{align}
$$
$P_{z}$ is a shortest path from $s$ to $z$
$$
\begin{align}
&\implies u_{1} u_{2} \dots u_{k} & \text{ is a shortest path from } s \text{ to } u_{k} \text{ (by Lemma 1)} \\
&\implies u_{1} u_{2} \dots u_{k-1} & \text{ is a shortest path from } s \text{ to } u_{k-1} \\
&\implies \delta(s, u_{k-1}) + w(u_{k-1}, u_{k}) = \delta(s, u_{k}) \\
&\implies d[u_{k}] \leq \delta(s,u_{k}) \\
&\implies d[u_{k}] \leq \delta(s,u_{k}) + l(p_{k,h}) & (\forall e \in E, w(e) \geq 0 \implies l(p_{k,h}) \geq 0) \\
&\implies d[u_{k}] \leq \delta(s,z) & (\delta(s,z) = \delta(s,u_{k}) + l(p_{k,h})) \\
&\implies d[u_{k}] < d[z]
\end{align}
$$

However, when $z$ was removed from $Q$,
$$
\begin{align}
d[z] &< d[w], \forall w \in Q \\
\implies d[z] &< d[u_{k}] & (\because u_{k} \text{ was still in } Q)
\end{align}
$$
Thus we have $d[u_{k}] < d[z]$ and $d[z] < d[u_{k}]$ (a contradiction!).
`\end{proof}`

##### Time complexity

> [!theorem|*]
> Algorithm Dijkstra runs in $O((|V| + |E|)\lg |V|)$ time.

`\begin{proof}`
Initializing the array $d[v], v \in V$, in the first two instructions takes $O(|V|)$ time.

The min-priority queue $Q$ can be implemented with a min-heap. Since the min-heap can be created in $O(|V|)$ time, the third instruction takes $O(|V|)$ time.

In creating the min-heap, we can create pointers $ptr[v]$ pointing at the node of $v$ in $Q$, for every $v \in V$, without affecting the $O(|V|)$ time bound (these pointers allow us to locate the node $v$ in $Q$ in $O(1)$ time in order to perform the `fixheap` operation on $v$).

Extracting the vertex with the smallest $d[\cdot]$ value from $Q$ takes $O(\lg n)$ time (heapsort). Each extract-min operation thus takes $O(\lg n)$ time.

Updating a $d[v]$ in the for each loop can be accomplished as follows:
- update $d[v]$ with $d[u] + w(u,v)$
- use $ptr[v]$ to locate the node of $v$ in the min-heap
- if $d[v] < d[parent(v)]$, then move $v$ upward along the root-to-$v$ path until a node at which the heap property is satisfied or the root is reached. Adjust the affected $ptr[\cdot]$'s accordingly.
The above steps can be done in $O(\lg |V|)$ time. Hence, updating a $d[v]$ value can be done in $O(\lg |V|)$ time.

For each iteration of the while loop, the instruction involving the extract-min operation takes $O(\lg |V|)$ time.

The for each loop is iterated once for each edge $e = (u,v)$. Since there are $deg_{G}(u)$ such edges $e$, the for each loop is executed $deg_{G}(u)$ times. The body of the for each loop takes $O(\lg |V|)$ time as it involves an update operation. Thus the for each loop takes $deg_{G}(u)O(\lg |V|)$ time.

The body of the while loop thus takes $O(\lg |V|) + \deg_{G}(u)O(\lg |V|)$ time. Since each vertex $u$ is removed from $Q$ once, and each edge $e=(u,v)$ is examined once at vertex $u$, the while loop thus takes
$$
\begin{align}
\sum_{u \in V} \left( O(\lg |V|) + \deg_{G}(u)O(\lg |V|) \right) &= O\left( \sum_{u \in V} \lg |V| \right) + O\left( \sum_{u \in V} \deg_{G}(u) \lg |V| \right) \\
&= O(|V|\lg |V|) + O(|E|\lg |V|) \\
&= O((|V| + |E|)\lg V) \text{ time.}
\end{align}
$$

Therefore, Algorithm Dijkstra runs in $O(|V|) + O(|V|) + O((|V| + |E|)\lg |V|)=O((|V| + |E|)\lg |V|)$ time.
`\end{proof}`
