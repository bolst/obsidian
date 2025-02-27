[[_Design and Analysis of Computer Algorithms]]

## T/F

1. $\sqrt{n} \in \Omega{\sqrt{2}^{\lg{n}}}$
2. $\lg{n} \in o(2^{\sqrt{\lg\lg n}})$
3. $O(n^2)\cdot O(n^3\lg{n}) \in O(\frac{n^6}{\lg{n}})$
4. $8^{\lg\lg n} \in O(\lg^4 n)$
5. $o(n) + \Omega(\sqrt n) \in O(n^2)$
6. $n^{5000} \in O((1.001)^n)$
7. $2^\sqrt{2\lg n} \in o(\sqrt{2}^{\lg n})$
8. $\log_{\sqrt[3]{n}} n \in \Omega(\lg\lg n)$
9. $o$ is a strict order


## Problems

1. Prove/disprove $\Theta(f) \cap o(f) = \emptyset$
2. Prove/disprove $f,g: \mathbb{N} \rightarrow \mathbb{R}_{\geq 0}. f\in O(g) \implies \lim_{n \rightarrow \infty} \frac{f(n)}{g(n)} = k \text{ for } k\geq 0$
3. Solve the following recurrence relations
	1. $T(n) = 3T(3n/4) + n^4 + \lg n$
	2. $T(n) = 5T(n/4) + n\lg\lg n$
	3. $T(n)=8T(n/4) + n\sqrt n \lg^2 n$
4. Solve the recurrence relation $T(mn) = km\lg (m)T(n) + O(mn)$ where $k>1$ and $m>2$

