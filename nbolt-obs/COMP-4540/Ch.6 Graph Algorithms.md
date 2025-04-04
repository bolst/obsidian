[[_Design and Analysis of Computer Algorithms]]


# 6.2 Computer representation of graphs

## Adjacency matrix

> [!definition|*] Adjacency Matrix
> Let $G=(V,E)$ be a simple graph, where $V=\{ v_{1},v_{2}, \dots, v_{n} \}$ and $|E|=m$. $G$ can be represented by a 2-dimensional array $A[1\dots n, 1\dots n]$, called the **adjacency matrix**, as follows:
> $$ \text{for } 1 \leq i, j \leq n, A[i,j] = \begin{cases}1 & \text{ if } \{ v_{i}, v_{j} \} \in E \\ 0 & \text{ if } \{ v_{i}, v_{j} \} \not\in E \end{cases} $$

## Adjacency list

> [!definition|*] Adjacency List
> Let $G=(V,E)$ be a simple graph, where $V = \{ v_{1},v_{2},\dots, v_{n} \}$ and $|E| = m$. $G$ can be represented by an array of linked lists, called the **adjacency lists**, as follows:
> 
> Let $L[1\dots n]$ be an array of pointers such that $L[i]$ points to a linked list containing the identifiers of all the vertices adjacent to vertex $v_{i}$.


# 6.3 Graph traversal

## Depth-first search

Starting from an arbitrary vertex $r$, arbitrarily pick an adjacent vertex and move to that vertex.

In general, at each vertex $v$, if $v$ has an unvisited adjacent vertex, move to that vertex and continue the search from there. If there is no unvisited adjacent vertex, then back up to the vertex from which vertex $v$ was first reached.

```python
Procedure DFS(v)
begin
	visit vertex v
	mark v as "visited"
	while there is an unvisited vertex w adjacent to v do
		DFS(w)
end
```

We shall use an adjacency list to represent $G=(V,E)$. Depth-first search can be modified as follows:

### Procedure DFS

```python
Procedure DFS(v)
begin
	visit vertex v
	mark v as "visited"
	ptr := L[v]
	while ptr != null do
		w := ptr.vertex
		if w is marked "unvisited" then
			DFS(w)
		ptr := ptr.next
end
```


#### Correctness

> [!lemma|6.1]
> Procedure DFS visits every vertex of $G$ once, and examines every edge of $G$ twice.

`\begin{proof}`
First we shall prove that DFS visits every vertex of $G$ once.

(Contradiction) Suppose the contrary that DFS does not visit every vertex of $G$. Let $U \subseteq V$ be the set of vertices not visited by DFS. Then $V-U$ is the set of vertices visited by DFS.

Let depth-first search begin with the procedure call `DFS(r)`. Then $r$ is visited, implying $r \in (V-U)$. Therefore $(V-U) \neq \emptyset$.

Since $G$ is connected, $\exists \{ u,v \} \in E_{G}$ such that $u \in (V-U)$ while $v \in U$.

Since $u \in (V-U)$, `DFS(u)` is invoked. During the execution of `DFS(u)`, the adjacency list of $u$ is processed. When the edge of $\{ u,v \}$ is being examined, $v$ is unvisited. Therefore, `DFS(v)` is invoked which makes $v$ visited. But $v\in U$ implies that $v$ is unvisited (a contradiction!). Hence, DFS visits every vertex of $G$.

For every vertex $v$, `DFS(v)` is invoked only if $v$ is unvisited, and $v$ is immediately marked "visited" after its first visit. Furthermore, once $v$ is marked visited, it carries that status until the end of the search. Therefore, every vertex $v$ is visited once.

Next we shall prove DFS examines every edge of $G$ twice.

Let $\{ u,v \} \in E_{G}$. $\{ u,v \}$ is examined when the adjacency list of $u$ or $v$ is processed. The adjacency list of $u$ is processed when $u$ is being visited. Since $u$ is visited once, $\{ u,v \}$is examined once at $u$.

Similarly, $\{ u,v \}$ is examined once at $v$. Therefore, every edge is examined twice, once from each of its end-vertices.
`\end{proof}`

#### Time complexity

> [!lemma|6.2]
> Procedure DFS takes $O(|V| + |E|)$ time.

`\begin{proof}`
The initial step of marking every vertex as "unvisited" takes $O(|V|)$ time.

Steps 1 and 2 take $O(1)$ time.

For each vertex $v$, the while loop, excluding the recursive call `DFS(w)`, takes $O(\deg(v))$ time. Moreover, DFS is called only once for $v$ by Lemma 6.1. Therefore, there will be no second call of `DFS(v)`.

The total time DFS spends on all the vertices in $G$ is thus $\sum_{v \in V}(O(1) + O(\deg(v)))$.

The total time complexity of depth-first search is thus:
$$
\begin{align}
& O(|V|) + \sum_{v \in V}(O(1) + O(\deg(v))) \\
&= O(|V|) + O(|V|) + O(2|E|) \\
&= O(|V| + |E|)
\end{align}
$$
`\end{proof}`


### Useful properties

A depth-first search of an undirected graph $G=(V,E)$ partitions the edge set $E$ of $G$ into two disjoint subsets $T$ and $B$ such that:
- $T$ consists of all the edges the depth-first search takes to move from a *visited* vertex to an *unvisited* vertex
	- Edges in $T$ are called **tree-edges**
- $B$ consists of the remaining edges (Therefore, an edge $e=\{ u,v \}$ belongs to $B$ iff when $e$ is examined for the first time, both end-vertices $u$ and $v$ are visited).
	- Edges in $B$ are called **back-edges**


> [!lemma|6.3]
> $T_{DFS}(V,T)$ is a spanning tree of $G=(V,E)$

`\begin{proof}`
$T_{DFS}$ is a spanning subgraph of $G$ because $T \subseteq E$ and the vertex set of $T_{DFS}$is that of $G$.

Suppose $T_{DFS}$ contains a cycle, say $C$. Among all vertices on $C$, let $w$ be the vertex that is visited last. Let $u$ and $v$ be the two vertices on $C$ that are adjacent to $w$. Without loss of generality, suppose $u$ is visited before $v$. Then, when depth-first search reaches $u$, vertex $w$ is unvisited. The fact that the edge $\{ u,w \}$ belongs to $C$ implies that when $\{ u,w \}$ is finally examined at $u$, $w$ remains unvisited. This in turn implies that $v$ also remains as unvisited. But then the search would advance from $u$ to $w$, making $w$ visited before $v$ (a contradiction)!.

Hence, $T_{DFS}$ contains no cycle.

Suppose $T_{DFS}$ is disconnected. Let $T'$ be a connected component of $T_{DFS}$, and $T''$ be the remaining part of $T_{DFS}$ after $T'$ is removed.

Since $T_{DFS}$ is a subgraph of $G$ and $G$ is connected, there exists an edge $\{ u,v \}$ in $G$ such that $u \in V_{T'}$ and $v \in V_{T''}$ (note that $\{ u,v \} \not\in T$).

Suppose DFS reaches $u$ before $v$. Since $u$ and $v$ belong to different connected components in $T_{DFS}$, there is no path connecting them in $T_{DFS}$. Therefore, when the edge $\{ u,v \}$ is being examined at $u$, $v$ remains as unvisited. As a result, DFS must have used $\{ u,v \}$ to move from $u$ to $v$, resulting in including $\{ u,v \}$ in $T$ (a contradiction!). Hence $T_{DFS}$ is connected.

Since $T_{DFS}$ is connected and cycle-free, it is thus a tree.
`\end{proof}`

> [!definition|*]
> Let $T'=(V',E')$ be a rooted tree, and $u,v \in V'$. Vertex $u$ is an **ancestor** of vertex $v$ iff $u$ lies on the path connecting $v$ and the root of $T'$. Vertex $u$ is a **proper ancestor** of vertex $v$ iff $u$ is an ancestor of $v$ and $u \neq v$. Vertex $v$ is a **proper descendent** of vertex $u$ iff vertex $u$ is a proper ancestor of vertex $v$.

> [!lemma|6.4]
> If $e=\{ v,w \} \in B$, then $v$ is an ancestor of $w$, or $w$ is an ancestor of $v$ in $\hat{T}_{DFS}$.


# 6.4 Connectivity

> [!definition|*] Problem
> Given an undirected graph $G=(V,E)$, determine all the connected components of $G$.

