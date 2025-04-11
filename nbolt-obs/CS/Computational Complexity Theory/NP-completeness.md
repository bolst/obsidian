[[_Computational Complexity Theory]]

# Definition

> [!definition|*] NP-complete
> A problem is **NP-complete** when:
> 1. It is a [[Decision Problem|decision problem]], meaning that for any input to the problem the output is either "yes" or "no".
> 2. When the answer is "yes", this can be demonstrated through the existence of a short (polynomial length) solution.
> 3. The correctness of each solution can be verified quickly (quickly means in polynomial time), and a brute-force search algorithm can find a solution by trying all possible solutions.
> 4. The problem can be used to simulate every other problem for which we can verify quickly that a solution is correct. In this sense, NP-complete problems are the hardest of problems to which solutions can be verified quickly. If we could find solutions of some NP-complete problem quickly, we could quickly find the solutions of every other problem to which a given solution can be easily verified.

More formally, we can define it as

> [!definition|*] NP-complete
> A decision problem $\Pi$ is **NP-complete** if
> 1. $\Pi \in \mathbf{NP}$
> 2. $\forall \Pi' \in \mathbf{NP}, \Pi' \propto \Pi$

^9998cc

Note that a decision problem is known as **NP-hard** if it satisfies at least condition 2. We can show that $\Pi \in \mathbf{NP}$ by verifying some candidate solution of $\Pi$ in polynomial time.


# Application

The easiest way we can prove a problem is NP-complete is to (1) prove it is $\in \mathbf{NP}$, and then to apply a reduction to some known NP-complete problem, such as
- [[Hamiltonian Path Problem]]
- [[Travelling Salesman Problem]]
- [[Subset Sum Problem]]
- [[Boolean Satisfiability Problem (SAT)]]

This is essentially saying that if we can solve one problem $\in \mathbf{NP}$ and then relate it (reduce it) to another problem $\in \mathbf{NP}$, we can solve the latter problem without *actually* going through the work to solve it independently.

A common method is to reduce [[Boolean Satisfiability Problem (SAT)#3-SAT|3-SAT]] to a given problem, demonstrated in the below example.

## Example: Subset Sum (SS)

> [!example|*]
> Prove that Subset Sum (SS) is NP-complete. SS is defined as:
> 
> Given a list of integers $S$ and a target sum $T$, are there any sublists of $S$ that sum precisely to $T$?

Assume that each $s_{i} \in S$ is less than $T$. 

We first prove that SS $\in \mathbf{NP}$. Given input $S=\{ s_{1}, s_{2}, \dots, s_{n} \}, T$ with solution $S'$, it takes $O(n \lg t)$ time to compute $\sum_{S} s_{i}$ and check if it equals $T$. Thus, since there is a polynomial-time algorithm to verify a solution to SS, then SS $\in \mathbf{NP}$.

Now we reduce 3SAT to SS.
Suppose we have a 3SAT formula $C = \bigwedge_{i=1}^m c_{i}$ with variables $X = \{ x_{1},x_{2},\dots, x_{n} \}$. Our input for SS will be $2n+2m$ numbers along with some $T$:
- Make $2n$ numbers for the literals $v_{1},v_{1}', v_{2}, v_{2}', \dots, v_{n}, v_{n}'$
- Make $2m$ numbers for the clauses $s_{1}, s_{1}', s_{2}, s_{2}', \dots, s_{m}, s_{m}'$

We will write each of our numbers and $t$ as an $n+m$ digit number (base 10).

> [!NOTE]
> We want to have $v_{i} \in S \iff x_{1} = true$ (or $v_{i}' \in S \iff x_{1} = false$) Thus we want $S$ to include exactly one of $\{ v_{i}, v_{i}' \}$.

For each variable $x_{i}$, we set digit $i$ of $v_{i}$ and $v_{i}'$ to 1.
For clause $c_{j}$, if $x_{i} \in c_{j}$ then set digit $n+j$ of $v_{i}$ to 1. Otherwise, if $\bar{x_{i}} \in c_{j}$, then set digit $n+j$ of $v_{i}'$ to 1.

We set digits 1 to $n$ of $t$ to 1. We set digits $n+1$ to $m$ of $t$ to 3.

Now we set $S_{i}, S_{i}'$ variables as "slacks" to complete the reduction. For each clause $c_{j}$, set digit $n+j$ of $S_{j}, S_{j}'$ to 1.

Since the first $n$ digits of $t$ are 1, then for each $i$, we can only choose at most one of $v_{i}, v_{i}'$ (since if we choose both, our numbers will sum to 2 instead of 1).

Since the last $m$ digits of $t$ are 3, then for each clause $c_{j}$, we will need to choose at least one number corresponding to a literal in $c_{j}$. For instance, if $c_{j} = x_{2} \lor \neg x_{4} \lor x_{7}$, then the only numbers that are non-zero in the $(n+j)$th digit are $v_{2}, v_{4}', v_{7}, s_{j}, s_{j}'$. To get this digit to sum to 3 we need at least one of $v_{2}, v_{4}', v_{7}$ to be in $S$.

For example, suppose our input (to 3SAT) is $C=(\neg x_{1} \lor \neg x_{2} \lor \neg x_{3}) \land (\neg x_{1} \lor \neg x_{2} \lor x_{3}) \land (x_{1} \lor \neg x_{2} \lor x_{3}) \land (x_{1} \lor x_{2})$. Then we create numbers for input to SS as shown in the table:

|          | $x_{1}$ | $x_{2}$ | $x_{3}$ | $c_{1}$ | $c_{2}$ | $c_{3}$ | $c_{4}$ |
|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|
| $v_{1}$  |    1    |         |         |         |         |    1    |    1    |
| $v_{1}'$ |    1    |         |         |    1    |    1    |         |         |
| $v_{2}$  |         |    1    |         |         |         |         |    1    |
| $v_{2}'$ |         |    1    |         |    1    |    1    |    1    |         |
| $v_{3}$  |         |         |    1    |         |    1    |    1    |         |
| $v_{3}'$ |         |         |    1    |    1    |         |         |         |
| $s_{1}$  |         |         |         |    1    |         |         |         |
| $s_{1}'$ |         |         |         |    1    |         |         |         |
| $s_{2}$  |         |         |         |         |    1    |         |         |
| $s_{2}'$ |         |         |         |         |    1    |         |         |
| $s_{3}$  |         |         |         |         |         |    1    |         |
| $s_{3}'$ |         |         |         |         |         |    1    |         |
| $s_{4}$  |         |         |         |         |         |         |    1    |
| $s_{4}'$ |         |         |         |         |         |         |    1    |
|   $t$    |    1    |    1    |    1    |    3    |    3    |    3    |    3    |

Finally, we prove that SS has a solution $\iff C$ is satisfiable.

$(\implies)$ Consider a solution $S$ for SS. We know that $S$ includes $v_{i}$ or $v_{i}'$ (but not both). If $v_{i} \in S$, then set $x_{i} = true$, otherwise if $\neg v_{i} \in S$, then set $x_{i} = false$. Thus we have an assignment. If we look at digit $n+j$, there will be at least one literal in $c_{j}$ that is satisfied.

$(\impliedby)$ Consider a satisfying assignment for $C$. If $x_{i} = true$, then add $v_{i}$ to $S$, otherwise if $x_{i} = false$ then add $v_{i}'$ to $S$. Then digit $i$ of our numbers will sum to 1, as desired.

Look at digit $n+j$: the assignment must satisfy at least one literal of $c_{j}$. If exactly one such literal is satisfied, then add $s_{j}$ and $s_{j}'$ to $S$. If exactly two are justified, add $s_{j}$ to $S$. If exactly three, then add neither $s_j$ nor $s_{j}'$.
`\end{proof}`

