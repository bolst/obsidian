[[_Design and Analysis of Computer Algorithms]]

# [[Ch.2 Sorting]]


# [[Ch.3 Order Statistics]]


# Exercises

## Misc

### $O(n)$ min/max

> [!exercise]
> Design an algorithm to find the maximum and minimum of an array in $O(n)$ time. Prove its correctness and time complexity.

The key idea here is to scan every element and keep track of the smallest and largest value seen. Since every element will be compared twice we should expect $2n$ comparisons.

```python
Algorithm MaxMin
Input: List L[1 ... n] of elements taken from a totally ordered set
Output: max and min of L
begin
	min := L[1]
	max := L[1]
	for i := 2 to n do
		if L[i] > max then
			max := L[i]
		if L[i] < min then
			min := L[i]
	return max, min
end.
```


#### Correctness

> [!lemma|1.1]
> At the end of the $(i-1)$th iteration of the for loop ($2 \leq i \leq n$), $max$ and $min$ will contain the maximum and minimum values, respectively, of the first $i$ elements in $L$ (i.e., $L[1 \dots i]$).

^118b50

`\begin{proof}`
(By induction on $i$).
**(Induction basis)** Before the 2nd iteration of the for loop, $max = min = L[1]$. At the end of the for loop ($i=1$), $max$ will be updated to $L[2]$ iff $L[2]>max=L[1]$, otherwise $max$ will remain as $L[1]$. In other words, at the end of the 1st for loop iteration $max=\max \{ L[1], L[2] \}$.

Also at the end of the for loop, $min$ will be updated to $L[2]$ iff $L[2] < min=L[1]$, otherwise $min$ will remain as $L[1]$. In other words, at the end of the 1st for loop iteration $min = \min \{ L[1], L[2] \}$.

**(Induction hypothesis)** Suppose this is true for $j=i$, where $2 \leq i \leq n$.

**(Induction step)** If $max,min$ denote the corresponding maximum and minimum values of $L[1 \dots j]$, then computing the maximum and minimum of $L[1 \dots j+1]$ (done at the $j$th iteration) is equivalent to computing $\max \{ \max (L[1 \dots j]), L[j+1] \}$ and $\min \{ \min (L[1 \dots j]), L[j+1] \}$. 

However, since the values of $max,min$ are the corresponding values for the maximum/minimum of $L[1\dots j]$, computing the maximum of $L[1 \dots j+1]$ is just equivalent to $\max \{ max, L[j+1] \}$ (similarly for minimum). Since we have two elements in this calculation, our base case applies and our induction hypothesis is proven true.
`\end{proof}`


> [!lemma|1.2]
> When the for loop completes all iterations, $max$ and $min$ will contain the maximum and minimum value, respectively, for $L[1 \dots n]$.

`\begin{proof}`
This follows directly from [[#^118b50]] for $i=n$.
`\end{proof}`

#### Time Complexity

> [!lemma|1.3]
> Algorithm **MaxMin** runs in $O(n)$ time.

`\begin{proof}`

The key operation here is comparison (specifically, $L[i] > max$ and $L[i] < min$).

Since there are two comparisons done at each iteration, each iteration will take $2$ time steps.

Since the for loop iterates over $i$ from $[2,n]$, the number of comparisons after the for loop completes is $\sum_{i=2}^{n} 2 = 2(n+1-2) = 2n-2$.

Therefore, the algorithm runs in $O(2n-2) = O(n)$ time.
`\end{proof}`

## Heapsort

### 6.1-1

> [!exercise]
> What are the minimum and maximum number of elements in a heap of height $h$?

Let the number of elements in a heap of height $h$ be $n$. A full binary tree has $2^{h+1}-1$ elements. 

Since a heap of height $h$ can have a minimum of 1 node at its lowest depth (highest level), the minimum number of elements is the full tree of height $h-1$ plus that additional node. Therefore the minimum number of elements are $(2^h-1) + 1 = 2^h$.

Since a heap of height $h$ that contains all its elements is equivalent to a full binary tree, the maximum number of elements is $2^{h+1} - 1$.

Therefore the number of elements in a heap of height $h$ is bounded by $2^h \leq n \leq 2^{h+1} - 1$.


### 6.2-6

> [!exercise]
> Show that the worst-case running time of [[Ch.2 Sorting#Algorithm Heapsort|FixHeap]] on a heap of size $n$ is $\Omega (\lg n)$ (solution available).

We know that a heap of height $h$ has $2^h \leq n \leq 2^{h+1}-1$ elements. Consider the two cases where $n$ is on the lower and upper bound:

**Case 1:** $n$ is on the lower bound
Then $n=2^h \implies h=\lg n$.
In this case, the worst case would be if the root element of the tree inputted to FixHeap was traversed all the way to the bottom of the tree, i.e., it traversed $h$ times. So in this case FixHeap runs in $\Omega (\lg n)$ time.

**Case 2**: $n$ is on the upper bound
Then $n=2^{h+1}-1 \implies h=\lg(n+1) - 1$.
This is similar to case 1, so FixHeap runs in $\Omega (\lg(n+1)-1)=\Omega(\lg n)$ time.

Thus, both bounds yield $\Omega (\lg n)$ time, so FixHeap must run in $\Omega (\lg n)$ time for the worst-case always.
`\end{proof}`


### 6.3-3

> [!exercise]
> Show that there are at most $\lceil n/2^{h+1} \rceil$ nodes of height $h$ in any $n$-element heap (solution available).

`\begin{proof}`
(By induction on the height $h$).

**(Induction basis)** For $h=0$, we must show there are $\lceil n/2^{h+1} \rceil = \lceil n/2 \rceil$ nodes with height $0$.



`\end{proof}`



### 6.4-4

> [!exercise]
> Show that the worst-case running time of [[Ch.2 Sorting#Algorithm Heapsort|Heapsort]] is $\Omega (n \lg n)$.


### 6-2

> [!exercise]
> A **$d$-ary heap** is like a binary heap, but (with one possible exception) non-leaf nodes have $d$ children instead of 2.
> 
> a. How would you represent a $d$-ary heap in an array?
> b. What is the height of a $d$-ary heap of $n$ elements in terms of $n$ and $d$?
> c. Give an efficient implementation of Heapsort in a $d$-ary max heap. Prove its correctness and runtime.
> 
>  (solution available)



## Quicksort


### 7.1-3

> [!exercise]
> Show that the running time of [[Ch.2 Sorting#Algorithm Quicksort|Split]] is $\Theta(n)$.


### 7.4-2

> [!exercise]
> Show that [[Ch.2 Sorting#Algorithm Quicksort|quicksort]]'s best-case running time is $\Omega (n \lg n)$ (solution available).



## Lower bounds on sorting

### 8.2-4

> [!exercise]
> Design an algorithm that, given a list of integers $L[1 \dots n]$ where $\forall x \in L, 0 \leq x \leq k$, preprocesses $L$ and answers any query about how many of the elements fall into a range $[a \dots b]$ in $O(1)$ time. Your algorithm should use $\Theta(n + k)$ preprocessing time (solution available).



## Order Statistics

### 9.3-7

> [!exercise]
> Describe an $O(n)$-time algorithm that, given a set $S$ of $n$ distinct numbers and a positive integer $k \leq n$, determines the $k$ numbers in $S$ that are closest to the median of $S$.



### 9.3-8

> [!exercise]
> Let $X[1 \dots n]$ and $Y[1 \dots n]$ be two arrays, each containing $n$ numbers already in sorted order. Give an $O(\lg n)$-time algorithm to find the median of all $2n$ elements in arrays $X,Y$ (solution available).








