[[_Design and Analysis of Computer Algorithms]]

# Problem Definition

Given a list of $n$ elements $(a_i)$ taken from a totally ordered set $(S,\precsim)$, find a permutation of the elements $(a_i')$ such that $a_i'\precsim a_{i+1}'$  


# Insertion Sort

Insertion sort involves taking each element of the sequence in order ($a_1, a_2,...$) and sorting all the elements that come before it.

## Algorithm Insertion-Sort
**Input:** An array of $n$ elements, $L[1...n]$, drawn from a totally ordered set.
**Output:** $L[1...n]$ sorted in ascending order.

```python
Algorithm InsertionSort
Input: An array of n elements L[1 ... n] drawn from a totally ordered set
Output: L[1 ... n] sorted in ascending order
begin
	for i := 2 to n do
		j := i - 1
		while L[j] > L[j+1] and j > 0 do
			swap L[j] and L[j+1]
			j := j - 1
		endwhile
end.
```

### Time Complexity

#### Worst-case

For each $i$, at most $i-1$ comparisons are performed.
Therefore
$$
\begin{align}
T(n) \leq \sum_{i=2}^n(i-1) &=\frac{(n+2)(n-1)}{2}-(n-1)\\
&= O(n^2)
\end{align}
$$

#### Average-case

Assume that (1) all elements in $L$ are distinct and (2) every $L[i]$ is equally likely to be in any one of the $i$ gaps in $L[1...i-1]$.

So the probability that $L[i]$ falls in any one of the $i$ gaps is $1/i$.

When $L[i]$ falls inside gap $j$, there are $i-j+1$ comparisons performed unless $j=1$, in which there are $i-1$ comparisons.

To insert $L[i]$ into its position will take

$$
\begin{align}
\sum_{j=2}^{i}\frac{1}{i}((i-j)+1) + \frac{1}{i}(i-1) &= \sum_{j=1}^i\frac{1}{i}j + \frac{1}{i}(i-1)\\
&= \frac{i+1}{2}-\frac{1}{i}
\end{align}
$$

and hence

$$
\begin{align}
T_{ave}(n) &= \sum_{i=2}^n\left(\frac{i+1}{2}-\frac{1}{i}\right)\\
&= \Theta(n^2)
\end{align}
$$

## Definition: In-place Algorithm

A sorting algorithm is said to be **in-place** if the amount of extra space it uses is $O(1)$.

Clearly, [[#2.1 Insertion Sort|insertion sort]] uses $O(1)$ extra space, so it is an in-place sorting algorithm.

## Definition: Permutation

A **permutation** on $\{1,2,...,n\}$ denoted by $\pi$, is a one-to-one function from $\{1,2,...,n\}$ to $\{1,2,...,n\}$.

A permutation $\pi$ can be expressed as

$$
\pi = \begin{pmatrix}
1 & 2 & 3 & \cdots & n \\
\pi(1) & \pi(2) & \pi(3) & \cdots & \pi(n)
\end{pmatrix}
$$

## Definition: Inverse of a Permutation

An **inverse** of a permutation $\pi$ is an ordered pair $(\pi(i),\pi(j))$ such that $i<j$ and $\pi(i)>\pi(j)$.

E.g., in the permutation 3 1 4 5 2, the inversions are $(3,1), (3,2), (4,2), (5,2)$.

Since comparing and swapping a pair of adjacent elements can eliminate at most one inversion, any sorting algorithm that sorts by comparing and swapping adjacent elements must make at least $k$ comparisons to sort an input list (permutation) with $k$ inversions.


# Quicksort

## Algorithm Quicksort

```python
Algorithm Quicksort(L, lower, upper)
Input: L[lower ... upper]
Output: L[lower ... upper] sorted in ascending order
begin
	if lower < upper then
		split(L, lower, upper, splitpoint)
		Quicksort(L, lower, splitpoint - 1)
		Quicksort(L, splitpoint + 1, upper)
end.

==================

Algorithm Split(L, lower, upper, var splitpoint)
Input: L[lower ... upper]
Output: splitpoint such that
				L[i] < L[splitpoint]   for lower <= i < splitpoint
				L[splitpoint] <= L[i]  for splitpoint < i <= upper
begin:
	x := L[lower]
	splitpoint := lower
	for index := lower + 1 to upper do
		if L[index] < x then
			splitpoint := splitpoint + 1
			swap L[splitpoint] and L[index]
	swap L[splitpoint] and L[lower]
end.

==================

Call Quicksort(L, 1, n)
```

Note that [[#Algorithm Quicksort|Split]] splits $L[lower ... upper]$ into two sublists $L[lower ... splitpoint - 1]$ and $L[splitpoint + 1 ... upper]$ such that:
$L[splitpoint]=x$, and $L[i]<x\leq L[j]$ where $lower\leq i < splitpoint$ and $splitpoint < j \leq upper$.

The last statement of [[#Algorithm Quicksort|Quicksort]] swaps the values in $L$ at the splitpoint and lower indices to create the desired sort.

