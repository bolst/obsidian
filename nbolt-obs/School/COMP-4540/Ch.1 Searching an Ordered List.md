[[_Design and Analysis of Computer Algorithms]]

# Master Theorem

> [!theorem|*] Master Theorem
> Let $T(n) = aT(n/b) + f(n), n \geq 0$ where $a \geq 1, b> 1$ are constants, $f(n)$ is a constant, and $n/b$ is interpreted as one of $\lceil  n/b  \rceil, \lfloor n/b \rfloor$.
> 
> 1. If $f(n) = O(n^{\log_{b}a - \varepsilon})$ for some $\varepsilon>0$, then $T(n) = \Theta(n^{\log_{b}a})$.
> 2. If $f(n) = \Theta(n^{\log_{b}a} \lg^k n), k\geq 0$, then $T(n) = \Theta(n^{\log_{b}a} \lg^{k+1} n)$.
> 3. If $f(n) = \Omega(n^{\log_{b} a + \varepsilon})$ for some $\varepsilon>0$, and satisfies the [[regularity property]] (i.e., if $af(n/b) \leq cf(n)$ for some $0<c<1$ and sufficiently large $n$), then $T(n) = \Theta(f(n))$.

