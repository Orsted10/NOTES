# 📘 GRAPH THEORY / VIVA QUESTIONS (60+ Questions with Detailed Answers)

---

## BASIC CONCEPTS (Q1–Q20)

**Q1. What is a graph?**
> A graph G = (V, E) is a collection of vertices (nodes) V and edges (connections) E. Each edge connects two vertices. It models relationships between objects.

**Q2. What is the difference between a vertex and an edge?**
> A vertex is a point/node representing an entity. An edge is a line/connection between two vertices representing a relationship.

**Q3. What is the difference between directed and undirected graphs?**
> In an undirected graph, edge (A,B) = edge (B,A) — the connection is mutual (like Facebook friendship). In a directed graph, edge (A,B) ≠ edge (B,A) — the connection is one-way (like Instagram follow).

**Q4. What is a weighted graph?**
> A graph where each edge has a numerical value (weight/cost) associated with it. Example: road map where edge weight = distance between cities.

**Q5. What is the degree of a vertex?**
> The number of edges connected to that vertex. For directed graphs, we separate into in-degree (edges coming IN) and out-degree (edges going OUT).

**Q6. What is the Handshaking Lemma?**
> The sum of degrees of all vertices = 2 × number of edges. Because each edge contributes 1 to the degree of each of its two endpoints.

**Q7. What is a path in a graph?**
> A sequence of vertices where each consecutive pair is connected by an edge. Example: A → B → C → D.

**Q8. What is a cycle?**
> A path that starts and ends at the same vertex. Example: A → B → C → A.

**Q9. What is a connected graph?**
> A graph where there exists a path between every pair of vertices. No vertex is isolated (unreachable).

**Q10. What is a complete graph?**
> A graph where every vertex is directly connected to every other vertex. Denoted K_n. A K_n has n(n-1)/2 edges.

**Q11. What is a tree in graph theory?**
> A connected, acyclic (no cycles) graph with exactly n-1 edges for n vertices. Every tree is a graph, but not every graph is a tree.

**Q12. What is a DAG?**
> Directed Acyclic Graph — a directed graph with no cycles. Used to represent task dependencies, course prerequisites, compilation order.

**Q13. What is a bipartite graph?**
> A graph whose vertices can be divided into two disjoint sets such that every edge connects a vertex from one set to the other. No edge within the same set.

**Q14. What is a sparse vs dense graph?**
> Sparse: few edges relative to vertices (E << V²). Dense: many edges (E ≈ V²). Most real-world graphs are sparse.

**Q15. What is a self-loop?**
> An edge that connects a vertex to itself.

**Q16. What is a multigraph?**
> A graph that allows multiple edges between the same pair of vertices.

**Q17. What is a subgraph?**
> A graph formed from a subset of vertices and edges of the original graph.

**Q18. What is adjacency?**
> Two vertices are adjacent if they are directly connected by an edge.

**Q19. How many edges can an undirected graph with n vertices have at most?**
> n(n-1)/2. This is the complete graph K_n. Example: 5 vertices → max 10 edges.

**Q20. How many edges can a directed graph with n vertices have at most?**
> n(n-1). Each pair can have two directed edges (A→B and B→A). Plus n self-loops = n² if self-loops allowed.

---

## REPRESENTATIONS (Q21–Q35)

**Q21. What are the two main ways to represent a graph?**
> Adjacency Matrix (2D array) and Adjacency List (array of linked lists).

**Q22. What is an adjacency matrix?**
> A 2D array of size V×V where matrix[i][j] = 1 if edge exists between vertex i and j, 0 otherwise. For weighted graphs, store the weight instead of 1.

**Q23. What is an adjacency list?**
> An array of linked lists. Each vertex has a list of all vertices it's connected to (its neighbors).

**Q24. What is the space complexity of adjacency matrix?**
> O(V²) — always, regardless of how many edges exist. Even for a graph with 0 edges, it uses V² space.

**Q25. What is the space complexity of adjacency list?**
> O(V + E) — proportional to vertices plus edges. Much better for sparse graphs.

**Q26. Which is better for checking if an edge exists?**
> Adjacency Matrix — O(1), just check matrix[i][j]. Adjacency List requires traversing the list — O(degree).

**Q27. Which is better for finding all neighbors of a vertex?**
> Adjacency List — O(degree), only visits actual neighbors. Adjacency Matrix requires checking all V columns — O(V).

**Q28. When should you use adjacency matrix?**
> Dense graphs (many edges), when you need O(1) edge lookup, small number of vertices, or weighted graphs where most pairs have edges.

**Q29. When should you use adjacency list?**
> Sparse graphs (few edges), large graphs, when you need to iterate over neighbors frequently, for BFS/DFS traversal.

**Q30. Is the adjacency matrix symmetric for undirected graphs?**
> Yes! matrix[i][j] always equals matrix[j][i] because edges are bidirectional.

**Q31. Is the adjacency matrix symmetric for directed graphs?**
> Not necessarily. Edge A→B doesn't imply edge B→A.

**Q32. What is the sum of all elements in an adjacency matrix for an undirected graph?**
> 2 × E (twice the number of edges), because each edge contributes 1 to matrix[i][j] AND matrix[j][i].

**Q33. How do you represent a weighted graph using adjacency matrix?**
> Use the weight value instead of 1. Use 0, -1, or ∞ (INT_MAX) for non-existent edges.

**Q34. How do you add a new vertex to adjacency matrix?**
> You need to create a new (V+1) × (V+1) matrix and copy all old values — O(V²). This is a major disadvantage.

**Q35. How do you add a new vertex to adjacency list?**
> Simply add a new entry with an empty linked list — O(1). Much easier!

---

## BFS & DFS (Q36–Q55)

**Q36. What is BFS?**
> Breadth-First Search — traverses graph level by level, visiting all neighbors before going deeper. Uses a Queue (FIFO).

**Q37. What is DFS?**
> Depth-First Search — traverses as deep as possible along one path before backtracking. Uses Stack/Recursion (LIFO).

**Q38. What data structure does BFS use?**
> Queue (FIFO — First In, First Out). Vertices are processed in the order they were discovered.

**Q39. What data structure does DFS use?**
> Stack (LIFO — Last In, First Out) or recursion (which implicitly uses the call stack).

**Q40. What is the time complexity of BFS and DFS?**
> Both are O(V + E) with adjacency list, O(V²) with adjacency matrix.

**Q41. What is the space complexity of BFS and DFS?**
> O(V) for the visited array. BFS also uses O(V) for the queue. DFS uses O(V) for the stack/recursion.

**Q42. Does BFS find the shortest path?**
> Yes, in unweighted graphs. BFS guarantees the shortest path (minimum number of edges) from source to any reachable vertex.

**Q43. Does DFS find the shortest path?**
> No. DFS finds A path, but not necessarily the shortest one.

**Q44. When would you use BFS over DFS?**
> When you need shortest path, level-order processing, or when the graph is wide but not deep.

**Q45. When would you use DFS over BFS?**
> When you need to explore all paths (backtracking), detect cycles, topological sort, or when the graph is deep.

**Q46. What is the difference between BFS and DFS traversal order?**
> BFS visits all vertices at distance 1, then distance 2, etc. DFS goes as deep as possible before backtracking.

**Q47. What is backtracking in DFS?**
> When DFS reaches a dead end (no unvisited neighbors), it returns to the previous vertex and tries a different path. This is automatic with recursion.

**Q48. Why do we need a visited array?**
> To prevent infinite loops in graphs with cycles. Without it, DFS/BFS would keep revisiting the same vertices forever.

**Q49. Can BFS and DFS be used on directed graphs?**
> Yes! Both work on directed and undirected graphs. The only difference is following directed edges instead of undirected ones.

**Q50. How to detect a cycle using DFS?**
> In undirected: if you visit an already-visited neighbor that is NOT the parent of the current vertex, there's a cycle. In directed: use a recursion stack — if you visit a vertex that's still in the current recursion path, there's a cycle.

**Q51. How to detect a cycle using BFS?**
> In undirected: if during BFS, a neighbor is already visited but is not the parent, there's a cycle.

**Q52. What is a BFS tree / DFS tree?**
> The edges used during BFS/DFS traversal form a tree called the BFS/DFS tree. It's a spanning tree of the connected component.

**Q53. How to find connected components using BFS/DFS?**
> Run BFS/DFS from vertex 0. All vertices visited belong to component 1. Find the next unvisited vertex, run BFS/DFS again → component 2. Repeat until all vertices are visited. Total components = number of BFS/DFS runs.

**Q54. How to check if a graph is bipartite using BFS?**
> Try to 2-color the graph: assign color 0 to start, color 1 to its neighbors, color 0 to their neighbors, etc. If you ever try to assign a different color to an already-colored vertex, it's NOT bipartite.

**Q55. What is topological sorting?**
> A linear ordering of vertices in a DAG such that for every directed edge (u, v), u comes before v. Done using DFS (process vertex after all its descendants are processed).

---

## ADVANCED (Q56–Q65)

**Q56. What is a spanning tree?**
> A subgraph that includes ALL vertices and is a tree (connected, no cycles, n-1 edges).

**Q57. What is a minimum spanning tree (MST)?**
> A spanning tree of a weighted graph with minimum total edge weight. Found using Prim's or Kruskal's algorithm.

**Q58. What is Dijkstra's algorithm?**
> An algorithm to find shortest paths from one source to all other vertices in a weighted graph with non-negative weights. Uses a priority queue (modified BFS).

**Q59. Difference between tree edge, back edge, forward edge, cross edge in DFS?**
> Tree edge: edge in DFS tree. Back edge: edge to an ancestor (indicates cycle). Forward edge: edge to a descendant (not in DFS tree). Cross edge: edge between non-ancestor/descendant vertices.

**Q60. What is a strongly connected graph?**
> A directed graph where there's a path from every vertex to every other vertex (in both directions).

**Q61. What is a weakly connected graph?**
> A directed graph that becomes connected when all edges are treated as undirected.

**Q62. What is an Euler path/circuit?**
> Euler path: visits every EDGE exactly once. Euler circuit: visits every edge exactly once and returns to start. Exists if 0 or 2 vertices have odd degree.

**Q63. What is a Hamiltonian path/circuit?**
> Hamiltonian path: visits every VERTEX exactly once. Hamiltonian circuit: visits every vertex exactly once and returns to start. NP-complete to determine.

**Q64. How much memory does an adjacency matrix use for a graph with 1000 vertices?**
> 1000 × 1000 × sizeof(int) = 1,000,000 × 4 = 4 MB. Even if the graph has only 10 edges! That's why adjacency list is preferred for sparse graphs.

**Q65. Can a disconnected graph have a spanning tree?**
> No! A spanning tree requires the graph to be connected. A disconnected graph has a spanning FOREST (one tree per component).

---

> **🎯 End of Theory Questions. 65 comprehensive VIVA questions covering every aspect of graphs. NEXT → Coding Questions!**
