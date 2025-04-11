[[_Design and Analysis of Computer Algorithms]]


# 8.1 Introduction

Most of the algorithms studied so far have been *polynomial time* algorithms: on input size $n$, their worst case complexities are $O(n^k)$ for some constant $k$.


> [!NOTE] 
> The subject of this chapter is an important class of (practical) problems whose status is unknown: no polynomial-time algorithm has been discovered for any of them, and no superpolynomial-time lower bound has been proven for any of them.

Some examples are:
1. The Traveling-Salesman Problem (TSP)
   Given a set of $n$ cities and the cost of travelling between every two cities, find a tour which starts from a city, passing through every other city once, and return to that same city, so that the total cost is minimum.
2. Hamiltonian Cycle (HC)
   Given an undirected graph $G=(V,E)$, does there exist a cycle in $G$ which includes every vertex of $G$?
3. CNF-Satisfiability (SAT)
   A *literal* is a logical variable (or the complement of a logical variable). A clause is a sequence of literals separates by the boolean operator $\vee$. A logical expression in *conjunctive normal form* (CNF) is a sequence of clauses separates by the boolean and operator $\wedge$.
   
   Given a logical expression in CNF, does there exist a way of assigning true and false values to the logical variables in the expression so the value of the expression is true?
4. Graph Colouring (GC)
   Given an undirected graph $G=(V,E)$, determine $\chi(G)$ (i.e., the smallest number of colours needed to colour $G$ such that no two adjacent vertices are given the same colour).

Some of the above are optimization problems, while the others are decision problems.

> [!definition|*] Optimization problem
> An optimization problem is a problem that seeks for the best among many solutions, according to a sample cost criterion (e.g., TSP and GC).

> [!definition|*] Decision problem
> A **decision problem** is a problem whose solution is either *yes* or *no* (e.g., HC and SAT).


# 8.2 The class P


> [!NOTE]
> For every optimization problem, there corresponds a decision problem.

For example, the Travelling-Salesman Problem has a corresponding decision problem "does there exist a tour which starts from a city, passing through every other city once, and return to that same city, so that the total cost incurred is no more than $K$?".

In general we can make the cast by imposing a bound on the value to be optimized.


> [!NOTE]
> A solution to an optimization problem gives rise to a solution to the corresponding decision problem.

For example, suppose the TSP is solved. We are to compute the solution of the TS decision problem for some input. Let $C$ be the optimal solution for the TSP for that input. Compare $C$ with $K$.

If $C\leq K$, the solution to the TS decision problem is yes.
If $C>K$, then since $C$ is the optimal (smallest) solution, there is no better one that could be less than $K$. So the solution to the TS decision problem is no.

In general we have the following.

> [!lemma|8.1]
> Let $\Pi$ be an optimization problem which can be solved in polynomial time. Then its corresponding decision problem $\Pi_{D}$ can also be solved in polynomial time.

`\begin{proof}`
First use a polynomial-time algorithm of $\Pi$ to produce a solution to $\Pi$.

Next, compare the solution with the bound provided in the problem definition of the corresponding decision problem $\Pi_{D}$. The solution to $\Pi_{D}$ can be determined.

As the comparison clearly can be done in polynomial time, the lemma thus follows.
`\end{proof}`


> [!NOTE]
> Optimization problems are no easier to solve efficiently than there corresponding decision problems.

Therefore, without loss of generality, we may centre the theory of [[NP-completeness]] around decision problems. Thus we can make a definition.

> [!definition|*]
> The complexity class $\mathbf{P}$ is the set of all decision problems that are solvable in polynomial time.


# 8.3 Impact of input size

Every decision problem can be considered as a set of *problem instances* in which each problem instance is either a yes-instance or no-instance.

Given an algorithm that solves a given decision problem, the inputs to that algorithm are the problem instances of that problem. So, the problem instances must be encoded in a way which is acceptable to the algorithm. A question that naturally arises is:

>Does the membership of a problem of class $\mathbf{P}$ depend on the encoding method used in encoding problem instances?

Unfortunately the answer is yes. For instance, consider the problem below.

## Example (Primality testing)

> [!example|*] Primality testing
> Given a positive integer $n$, is $n$ a prime number?
> 

The following is an algorithm for Primality testing.

```python
Algorithm PrimalityTest
Input: an integer n
Output: yes iff n is a prime
begin
	found := false
	j := 2
	while not(found) and j < n do
		if n%j = 0 then
			found := true
		else
			j := j + 1
	return not(found)
end
```


### Encoding method for input

1. *unary numbering*: input size is $k=n$.
   Each iteration of the while loop takes $O(n)$ time because doing division with unary numbers takes $O(n)$ time. The while loop thus takes $O(n^2)$ time. The total time is thus $O(n^2) = O(k^2)$.
   Hence, Primality Testing belongs to $\mathbf{P}$.
2. *binary numbering*: input size is $k=\lg n$.
   Each iteration of the while loop takes $O(1)$ time. The while loop thus takes $O(n)$ time. The total time is thus $O(n)=O(2^{\lg n}) = O(2^k)$. Therefore, algorithm PrimalityTest takes exponential time, so we cannot claim that Primality Testing belongs to $\mathbf{P}$.

Hence, the membership of a decision problem of $\mathbf{P}$ depends on the encoding method used in representing its input.

We also can define "reasonable" encodings:
1. Numbers in binary form, or in number systems that are polynomially related to the binary system.
2. Characters represented by binary-codes (e.g., ASCII).
3. Representation of finite sets is polynomially related to its representation as a list of its elements enclosed within braces separates by commas.
4. Tuples, graphs, functions, etc. are represented as finite sets or collections of finite sets.


# 8.4 The class NP

Although many decision problems have no known polynomial time algorithm solving them, they do have polynomial time verification algorithms that *verifies* their yes-instances.

For example, on a Hamiltonian Cycle (HC), no polynomial time algorithm is known. However, HC does have a polynomial time verification algorithm.

Given a graph $G=(V,E)$ and a sequence of vertices $C$ which form a HC in $G$, the verification algorithm examines $C$ to make sure that:
- the first and last vertices in $C$ are identical
- every vertex of $G$ appears in $C$ exactly once (with the exception of the first and last)
- there is an edge connecting every two consecutive vertices in $C$

```python
Input: A graph G=(V,E) and a sequence of vertices C=v[1 ... |C|] in G
Output: Yes if C is a Hamiltonian cycle of G, otherwise "anything but yes" is returned, since we dont know if the algorithm can terminate
begin
1	V0 = V
2	if v[1] = v[|C|] and |V| + 1 = |C| then
		for i := 1 to |C| - 1 do
			if v[i] is in V0 and (v[i], v[i+1]) is in E then
				V0 = V0 - { v[i] }
			else
				break
		return 'yes'
end
```

Step 1 takes $O(1)$ time.
For step 2, the if statement in the for loop takes $O(|V| + |E|) = O(|V|^2)$ time. The entire step thus takes $O(|C| \times |V|^2) = O(|V|^3)$ time.
Thus the total time is $O(|V|^3)$ and HC has a polynomial-time verification algorithm.

> [!definition|*] Nondeterministic algorithm
> A **nondeterministic algorithm** for a decision problem $\Pi$ is an algorithm which operates in two phases:
> 1. (The (nondeterministic) guessing phase)
>    An arbitrary string of characters $s$, called a **guess**, is written at some designated place in memory.
> 2. (The verification phase)
>    A deterministic algorithm begins its execution with the input $\pi$ and the guess string $s$ (it may completely ignore $s$) and will (a) eventually halt and output "yes" if $\pi$ is a yes-instance, or (b) eventually halt and output "no" or never halt if $\pi$ is a no-instance.

> [!definition|*] Certificate
> A **certificate** is a guess which leads to a "yes" output.
> So, a certificate contains conclusive evidence to the effect that the given problem instance $\pi$ is a yes-instance if it is indeed a yes-instance.


Moreover, the nondeterministic algorithm has a "magical" insight so that whenever the given problem instance $\pi$ is a yes-instance, the guess $s$ written in phase 1 is always a certificate.

> [!definition|*]
> The **execution time of a nondeterministic algorithm** is the time it takes to write down the guess string $s$ plus the execution time of the verification phase, i.e.,
> $$ O(|s|) + \text{ execution time of verification phase } $$

> [!definition|*]
> The complexity class $\mathbf{NP}$ is the set of all decision problems which can be solved by nondeterministic algorithms in polynomial time.

Intuitively, $\mathbf{P}$ consists of problems that can be *solved* quickly, where $\mathbf{N}\mathbf{P}$ consists of problems that can be *verified* quickly.

## Example: TSP

> [!example|*]
> Show that TSP $\in \mathbf{N}\mathbf{P}$

Given algorithm:
```python
nondeterministic-Algorithm TSP
Input: 
	a set of n cities C = {1, 2, ..., n}
	a matrix c[1 ... n, 1 ... n], c[i,j] is the cost of travelling from city i to city j
	a positive real number K
Output: yes if there is a tour with total cost <= K (otherwise there is no specified output)
begin
	# guessing phase
	write down a sequence of cities t[1 ... |T|]
	
	# verification phase
	C0 := C
	cost := 0
	if t[1] = t[|T|] and |T| = n + 1 then
		for i := 1 to |T| - 1 do
			if t[i] is in C0 then
				C0 := C0 - { t[i] }
				cost := cost + c[t[i], t[i+1]]
			else
				break
	return 'yes' if cost <= K else 'no'
end
```

The algorithm takes $O(n^{2})$ time, thus TSP $\in \mathbf{N}\mathbf{P}$.

> [!lemma|8.2]
> $\mathbf{P} \subseteq \mathbf{N}\mathbf{P}$.

^a16836

`\begin{proof}`
Let $\Pi \in \mathbf{P}$. There exists a polynomial time algorithm $A$ which solves $\Pi$. Construct a nondeterministic algorithm $A'$ in which the guessing phase writes down the null string, and the verification phase ignores the guess string and uses $A$ as the verification algorithm to execute just on the given input $\pi$.

Then $\pi \in \Pi$ is a yes-instance $\iff A$ halts on input $\pi$ and prints a "yes" $\iff A'$ halts on input $\pi$ and prints a "yes".

In other words, $A'$ is a verification algorithm for $\Pi$. Since the guessing phase of $A'$ takes $O(1)$ time and the verification phase of $A'$ takes polynomial time, $A'$ thus takes polynomial time, i.e., $\Pi \in \mathbf{N}\mathbf{P}$. Hence, $\mathbf{P} \subseteq \mathbf{N}\mathbf{P}$.
`\end{proof}`


> [!remark|*]
> An open problem is, is it true that $\mathbf{P} = \mathbf{N}\mathbf{P}$? 
> Most researchers believe that $\mathbf{P} \neq \mathbf{N}\mathbf{P}$.



# 8.5 NP-Complete problems

First, we shall introduce a technique called *reduction*, which transforms one problem to another so that a solution to the latter provides a solution to the former.

Intuitively, the concept of reduction between decision problems can be explained as follows.

Let $\Pi_{1}$ be a problem we want to solve, and $\Pi_{2}$ be a problem for which we have an algorithm $A_{2}$.

Suppose we have an algorithm $T$ which takes a problem instance $\pi_{1} \in \Pi_{1}$ and produces a problem instance $T(\pi_{1}) \in \Pi_{2}$ such that:
$$
\pi_{1}\text{ is a yes-instance of } \Pi_{1} \iff T(\pi_{1})\text{ is a yes-instance of } \Pi_{2}
$$
Then, by composing $T$ and $A_{2}$, we obtain an algorithm $A_{1}$ for $\Pi_{1}$.

In the theory of $\mathbf{N}\mathbf{P}$-completeness, an additional requirement for $T$ is that is has to be efficient. We thus have the following definition.

> [!definition|*]
> Let $\Pi_{1},\Pi_{2}$ be two decision problems, and $T: \Pi_{1} \to \Pi_{2}$.
> $T$ is a **polynomial reduction** (or **polynomial transformation**) from $\Pi_{1}$ to $\Pi_{2}$ if:
> (i) $T$ can be computed in polynomial time
> (2) $\forall \pi_{1} \in \Pi_{1}$, $\pi_{1}$ is a yes-instance of $\Pi_{1} \iff T(\pi_{1})$ is a yes-instance of $\Pi_{2}$.
> 
> $\Pi_{1}$ is **polynomially reducible** to $\Pi_{2}$, denoted by $\Pi_{1} \propto \Pi_{2}$, if there exists a polynomial reduction from $\Pi_{1}$ to $\Pi_{2}$.

> [!example|*]
> Let $\Pi_{1}$ be: Given $n$ boolean values $v_{1},v_{2},\dots,v_{n}$, is at least one of the $v_{i}$'s true?

`\begin{proof}`
Let $\Pi_{2}$ be: Given $n$ integers $x_{1}, x_{2}, \dots, x_{n}$, is the maximum integer positive?
Define $T: \Pi_{1} \to \Pi_{2}$ such that $\forall(v_{1}, v_{2}, \dots, v_{n}) \in \Pi_{1}$, $T((v_{1},v_{2}, \dots, v_{n})) = (x_{1},x_{2}, \dots, x_{n})$ where
$$
x_{i} = \begin{cases}
1 & \text{ if } v_{i} \text{ is true } \\
0 & \text{ if } v_{i} \text{ is false }
\end{cases}
$$
$\pi_{1} \in \Pi_{1}$ is a yes-instance $\iff \exists v_{i}, v_{i}$ is true $\iff \exists x_{i}, x_{i} = 1 \iff T(\pi_{1}) \in \Pi_{2}$ is a yes-instance.

Moreover, the function $T$ can be easily computed in $O(n)$ time. Hence, $\Pi_{1} \propto \Pi_{2}$.
`\end{proof}`

> [!lemma|8.3]
> Let $\Pi_{1} \propto \Pi_{2}$. Then $\Pi_{2} \in \mathbf{P} \implies \Pi_{1} \in \mathbf{P}$.

^f6e54f

`\begin{proof}`
$\Pi_{1} \propto \Pi_{2}$ implies that there exists a polynomial reduction $\mathcal T$ from $\Pi_{1}$ to $\Pi_{2}$. Let $T$ be the polynomial-time algorithm computing $\mathcal{T}$.

$\Pi_{2} \in \mathbf{P}$ implies that there exists a polynomial time algorithm $A_{2}$ solving $\Pi_{2}$.

Consider the following algorithm $A_{1}$:
1. Execute algorithm $T$ on input $\pi_{1} \in \Pi_{1}$ to produce $\mathcal{T}(\pi_{1}) \in \Pi_{2}$
2. Execute algorithm $A_{2}$ on input $T(\pi_{1}) \in \Pi_{2}$ (the output of $A_{2}$ is the output of $A_{1}$).
   So $A_{1}$ outputs a yes on input $\pi_{1} \in \Pi_{1} \iff A_{2}$ outputs a yes on input $\mathcal{T}(\pi_{1}) \in \Pi_{2} \iff \mathcal{T}(\pi_{1})$ is a yes-instance $\iff \pi_{1}$ is a yes-instance.
Therefore, algorithm $A_{1}$ solves $\Pi_{1}$.

Now, suppose for any input of size $n$, algorithm $T$ takes $O(p(n))$ time, and algorithm $A_{2}$ takes $O(q(n))$ time, where both $p,q$ are polynomial functions.

Since writing each symbol of $\mathcal{T}(\pi_{1})$ takes $O(1)$ time, the length of $\mathcal{T}(\pi_{1})$ is bounded by the execution time of $T$. In other words
$$
|\mathcal{T}(\pi_{1})| = O(p(n))
$$
Algorithm $A_{1}$ thus takes $O(q(p(n))) = O((p \circ g)(n))$ time. As $p,q$ are polynomial functions, so is $p \circ q$. Hence, $\Pi_{1} \in \mathbf{P}$.
`\end{proof}`

The above Lemma can be equivalently stated as
$$
(\Pi_{1} \propto \Pi_{2} \implies \Pi_{1} \not\in \mathbf{P}) \implies \Pi_{2} \not\in \mathbf{P}
$$
Hence, $\Pi_{1} \propto \Pi_{2}$ can be interpreted as "$P_{2}$ is at least as hard to solve efficiently as $\Pi_{1}$".


> [!definition|*] NP-Complete
> A decision problem $\Pi$ is **NP-complete** if
> (i) $\Pi \in \mathbf{N}\mathbf{P}$
> (ii) $\forall \Pi' \in \mathbf{N}\mathbf{P}, \Pi' \propto \Pi$

Informally speaking, NP-complete problems are the hardest problem to solve efficiently in $\mathbf{N}\mathbf{P}$.

> [!theorem|8.4]
> Let $\Pi$ be any $\mathbf{N}\mathbf{P}$-complete problem. If $\Pi \in \mathbf{P}$, then $\mathbf{P} = \mathbf{N}\mathbf{P}$.

`\begin{proof}`
Let $\Pi' \in \mathbf{N}\mathbf{P}$.
Then, $\Pi$ is $\mathbf{N}\mathbf{P}$-complete $\implies \Pi'' \in \mathbf{N}\mathbf{P}, \Pi'' \propto \Pi \implies \Pi' \propto \Pi$.
But $\Pi \in \mathbf{P}$, therefore by [[#^f6e54f]], $\Pi' \in \mathbf{P}$, i.e., $\mathbf{N}\mathbf{P} \subseteq \mathbf{P}$.
But by [[#^a16836]], $\mathbf{P}\subseteq \mathbf{N}\mathbf{P}$. Hence, $\mathbf{P} = \mathbf{N}\mathbf{P}$.
`\end{proof}`


# 8.6 Proving NP-completeness

> [!definition|*] Truth assignment
> A **truth assignment** of a CNF logical expression is a function $t: U \to \{ \text{true}, \text{false} \}$, where $U$ is the set of variables in the expression. Let $u \in U$. If $t(u) =\text{true}$, then $u=\text{true}$ under $t$, and $\bar{u} =\text{false}$ under $t$. A CNF expression is **satisfiable** if there exists a truth assignment $t$ for the expression so that the value of the expression is true.

For example, the following is a satisfiable CNF logical expression
$$
(\bar{p} \vee q) \wedge (p \vee r) \wedge (q \vee \bar{r} \vee \bar{s})
$$
For the following assignment of truth values to the variables
$$
t(p) = \text{false}; \ \ \ t(q) = \text{true}; \ \ \ t(r) = \text{true}; \ \ \ t(s) = \text{true}
$$
So thus $(\bar{p} \vee q) \wedge (p \vee r) \wedge (q \vee \bar{r} \vee \bar{s}) = \cdots =\text{true}$.
Therefore, the expression is satisfiable.


> [!lemma|8.5]
> $\Pi_{1} \propto \Pi_{2} \wedge \Pi_{2} \propto \Pi_{3} \implies \Pi_{1} \propto \Pi_{3}$.

> [!corollary|8.5.1]
> Let $\Pi'$ be an $\mathbf{N}\mathbf{P}$-complete problem, and $\Pi \in \mathbf{N}\mathbf{P}$. Then
> $$ \Pi' \propto \Pi \implies \Pi\text{ is also } \mathbf{N}\mathbf{P}\text{-complete} $$

The above corollary leads us to the following method of proving $\mathbf{NP}$-completeness.

> [!remark|*]
> To prove that a decision problem $\Pi$ is $\mathbf{NP}$-complete, we may proceed as follows:
> 1. Prove that $\Pi \in \mathbf{NP}$
> 2. Select a known $\mathbf{NP}$-complete problem $\Pi'$
> 3. Show that $\Pi' \propto \Pi$


Note that we have only covered (so far) that the CNF Satisfiability problem (SAT) is the only known $\mathbf{NP}$-complete problem. Construction of the rest of the examples can begin from SAT. The follow diagram is the sequence of reductions we can use to prove the $\mathbf{NP}$-completeness of the respective problems

```mermaid
graph TD
	SAT --> 3SAT
	3SAT --> ...
	3SAT --> 3GC
	3SAT --> VC
	VC --> HC
	HC --> TSP
```


`\end{proof}`



# 8.7 Coping with NP-complete/hard problems

A decision problem is NP-complete (or an optimization problem is NP-hard) indicates that it is *highly unlikely* that we can find a *polynomial-time algorithm* solving that problem.

Suppose you are given a problem that has no known polynomial-time algorithm solving it (regardless of whether it is NP-complete/hard), and whose practical application is so important that you must find an efficient algorithm solving it. How would you handle such a situation?

There are several possible approaches.

## Hoping for the best

This involves developing the most efficient exponential-time algorithm for the problem.
- If the actual input sizes are small, the execution time of the exponential-time algorithm may be acceptable
- Since the time complexity derived for the algorithm is a *worst-case* measure, the (worst-case) inputs that make the performance of the algorithm to be unacceptable may never occur in practice
  (For example, the *simplex* method algorithm used in Linear Programming has an exponential worst-case time complexity, but has had an excellent record in solving problem instances quickly in practice)


## Study the inputs

Perform an empirical study on the problem to find out the set of inputs which occur usually in practice. Design an algorithm which works well for that set of inputs.

For instance, some graph-theoretic problems are NP-hard but if the input is restricted to planar or interval graphs, polynomial-time algorithms solving them do exist. To make use of these algorithms, one would have to design recognizing algorithms. A *recognizing algorithm* for a class of graphs outputs a "yes" if the input graph is in the class, and "no" otherwise.


## Backtracking

**Backtracking** is a method that can be used when we want to solve an NP-complete decision problem, or to find *all* feasible solutions of an NP-hard optimization problem.

Conceptually, the method traverses a *labeled rooted tree* $T_{I}$ associated with the given (input) instance $I$.

The tree $T_{I}$, called a *solution space tree*, represents the solution space of $I$. There are two types of solution space trees: *static* and *dynamic*. We shall consider the former type below.
- At level $k, 0 \leq k < n$, every vertex $v$ is associated with a distinct $k$-tuple $(s_{1}, s_{2}, \dots, s_{k})$, where $s_{i} \in S_{i}, 1 \leq i \leq k$, such that each $S_{i}$ is a finite set and vertex $v$ has $|S_{k+1}|$ children each of which is associated with a distinct $(k+1)$-tuple $(s_{1},s_{2}, \dots, s_{k+1})$, where $s_{k+1} \in S_{k+1}$.
- A $k$-tuple represents a partial solution of $I$, if it is associated with an internal vertex, and represents a complete solution (may or may not be feasible) if it is associated with a leaf.

Notice that $T_{I}$ is not explicitly constructed. So, conceptually, a backtracking algorithm starts from the root of $T_{I}$ and performs a depth-first search over the three. At each internal vertex $v$, if $v$ is on level $k$, then a partial solution $(s_{1},s_{2},\dots,s_{k})$ has been determined at $v$.

The algorithm would try and extend the partial solution by appending an element $a \in S_{k+1}$ to it. If it can be determined that the resulting tuple $(s_{1},s_{2}, \dots , s_{k}, a)$ is not potentially extensible to a feasible solution for $I$, then the algorithm will try again with another element $b \in S_{k+1}$.

If there is no other element $b$ in $S_{k+1}$, then the last element $s_{k}$ in the partial solution is removed from $(s_{1},s_{2},\dots, s_{k})$.

On the other hand, if $(s_{1},s_{2},\dots,s_{k},a)$ is potentially extensible to a feasible solution for $I$, then the search advances to the child associated with $(s_{1},s_{2},\dots,s_{k},a)$.

When the search reaches a leaf corresponding to an $n$-tuple $(s_{1},s_{2},\dots, s_{n})$, if it is a feasible solution for $I$, then it is added to the set of feasible solutions found so far, or is used to update the best solution found so far if $I$ is an instance of an optimization problem.

Notice that the algorithm essentially performs an *exhaustive search* over the solution space of $I$ although it may not traverse the entire $T_{I}$. As a consequence,
- the algorithm has a worst-case exponential time complexity
- the algorithm will always find the set of all feasible solutions, or an optimal solutions


## Branch and bound

This method is based on backtracking. The key idea is to speed up backtracking by maintaining a lower (upper, respectively) bound on the optimal cost of the given maximization (minimization) problem so that when the search reaches a vertex $v$, if one can determine that no feasible solution within $T_{v}$ has a cost higher (lower) than the lower (upper) bound, then $T_{v}$ contains no optimal solution.

As a result, the search can immediately backtrack to the parent of $v$. In this way, the time that is needed to traverse $T_{v}$ is saved.


## Lower the worst-case exponential-time complexity

Design an algorithm with a *slowly increasing* worst-case exponential-time complexity. This time complexity is substantially smaller than that of the brute force method.

For instance, if there are $2^n$ feasible solutions for an input instance of size $n$, then a brute-force method would take $\Theta(2^n)$ time.

Using this approach, we may aim at designing an algorithm that has an exponential time complexity $O(c^n)$ for some $1<c<2$.

Although an algorithm with an $O(c^n)$ time complexity is still an exponential algorithm, the largest input size it could handle *in practice* could be significantly larger than that of any algorithm based on a brute force method.

Similarly, for optimization problems that have $n!$ feasible solutions for input size $n$, we may aim at designing an algorithm that has exponential time complexity $O(2^n)$ (notice $2^n \in o(n!)$).


## Parameterized complexity

The basic idea is to look for a parameter $k$ to design an algorithm that has a polynomial-time complexity in the input size $n$ but not in the value of $k$.

An algorithm with such time complexity is called a **parameterized polynomial-time algorithm**.

For small $k$ values, the time complexity could be polynomial.

For example consider $O(2^{k^{2}}n^2)$. For $k=\sqrt{ \lg n }, O(2^{k^{2}}n^{2})=O(n^3)$ which is polynomial. For $k=\sqrt{ n }, O(2^{k^{2}}n^{2}) = O(2^n n^{2})$ which is exponential.

If most of the *actual* problem instances (inputs) of the optimization problem under consideration have reasonably small values for the parameter $k$, then we have an efficient algorithm for the problem.

Notice that based on $k$, we can partition the set of all input instances of an optimization problem into disjoint classes so that the problems belonging to the same class have the same/similar $k$ values, indicating they may have similar computational hardness.

## Local search

In contrast with branch and bound, the local search method *does not* search the entire solution space but a restricted area only.

Therefore it may not find an optimal solution, but a local optimum because the restricted area it searches for may contain no optimal solution. To compensate, the local search method is usually very efficient.

To design a local search algorithm, one has to first define a local transformation on the set of all feasible solutions $M(I)$ of the input instance $I$. The local transformation converts one feasible solution to another feasible solution by making some local (small) changes to the specification of the formal.

Conceptually, the local transformation creates a graph $G_{M(I)} = (M(I), E)$ such that $\forall s,s' \in M(I), (s,s') \in E \iff$ there is a local transformation from $s$ to $s'$.

Then the local search algorithm starts from a vertex (feasible solution) $s$ of $G_{M(I)}$ and moves to an adjacent vertex (feasible solution) $s'$ via an edge $(s,s')$ such that
$$
\begin{cases}
cost (s') < cost (s) & \text{for minimization problem} \\
cost (s') > cost (s) & \text{for maximization problem}
\end{cases} 
$$
Next, $s'$ becomes $s$ and the above step is repeated. The search halts at a vertex where there is no adjacent vertex with improved cost value. This may or may not be an optimal solution.

