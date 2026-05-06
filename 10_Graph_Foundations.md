# 📘 GRAPH FOUNDATIONS — What Are Graphs? (Ultra Detailed)

---

## 1.1 — What is a Graph? (Starting from Zero)

### 🧠 Real-Life Analogy
Think of a **map of cities connected by roads**.
- Each **city** = a **Vertex** (also called Node)
- Each **road** between two cities = an **Edge** (also called Link/Arc)

```
    Delhi ──────── Jaipur
      │               │
      │               │
    Agra ──────── Lucknow
```
This IS a graph! 4 vertices (cities), 4 edges (roads).

Another analogy — **Facebook friendships**:
- Each **person** = Vertex
- Each **friendship** = Edge (if A is friends with B, there's an edge between them)

### 📖 Formal Definition
> **A Graph G is a pair G = (V, E) where:**
> - **V** = a finite, non-empty set of **Vertices** (nodes/points)
> - **E** = a set of **Edges** (connections/links between vertices)
> - Each edge connects TWO vertices

```
Example: G = (V, E)
V = {A, B, C, D}
E = {(A,B), (A,C), (B,D), (C,D)}

Visual:
    A ─── B
    │     │
    C ─── D
```

---

## 1.2 — Graph Terminology (EVERY Term Explained)

### Vertex (Node/Point)
> A fundamental unit of a graph. Represents an entity.
- In a social network: a person
- In a road map: a city  
- In a computer network: a computer
- **Notation:** Usually named as integers (0, 1, 2, 3...) or letters (A, B, C...)

### Edge (Link/Arc)
> A connection between two vertices.
- **Notation:** Written as a pair (u, v) meaning "edge from vertex u to vertex v"
- Edge (A, B) means A and B are connected

### Degree of a Vertex
> The number of edges connected to a vertex.

```
    A ─── B
    │     │
    C ─── D
    │
    E

degree(A) = 2  (connected to B and C)
degree(B) = 2  (connected to A and D)
degree(C) = 3  (connected to A, D, and E)
degree(D) = 2  (connected to B and C)
degree(E) = 1  (connected to C only)
```

**Important Formula:** Sum of all degrees = 2 × Number of edges
(Because each edge contributes 1 to the degree of EACH of its two endpoints)

### In-Degree and Out-Degree (For Directed Graphs Only)
- **In-Degree:** Number of edges COMING INTO a vertex
- **Out-Degree:** Number of edges GOING OUT of a vertex

```
    A ──→ B
    │     ↑
    ↓     │
    C ──→ D

in-degree(A) = 0   out-degree(A) = 2   (A sends to B and C)
in-degree(B) = 1   out-degree(B) = 0   (B receives from A)
in-degree(C) = 1   out-degree(C) = 1   (C receives from A, sends to D)
in-degree(D) = 1   out-degree(D) = 1   (D receives from C, sends to B)
```

### Path
> A sequence of vertices where each adjacent pair is connected by an edge.

```
    A ─── B ─── C ─── D

Path from A to D: A → B → C → D (length 3, i.e., 3 edges)
Another path: A → B → C (from A to C, length 2)
```

### Cycle
> A path that starts and ends at the SAME vertex.

```
    A ─── B
    │     │
    C ─── D

Cycle: A → B → D → C → A (starts at A, ends at A)
```

### Connected Graph
> A graph where there IS a path between every pair of vertices.
```
CONNECTED:           NOT CONNECTED (has 2 components):
  A ─ B                A ─ B     D ─ E
  │   │                │         │
  C ─ D                C         F
```

### Complete Graph (K_n)
> A graph where EVERY vertex is connected to EVERY other vertex.
```
K4 (complete graph with 4 vertices):
    A ─── B
    │ ╲ ╱ │
    │  ╳  │
    │ ╱ ╲ │
    C ─── D

Total edges in K_n = n(n-1)/2
K4 = 4×3/2 = 6 edges
```

### Weighted Graph
> A graph where each edge has a numerical VALUE (weight/cost).
```
    A ──5── B
    │       │
    2       7
    │       │
    C ──3── D

Weight of path A→B→D = 5 + 7 = 12
Weight of path A→C→D = 2 + 3 = 5  ← SHORTER!
```

### Directed vs Undirected Graph

| Feature | Undirected | Directed |
|---------|-----------|----------|
| Edge notation | (A, B) = (B, A) | (A, B) ≠ (B, A) |
| Visual | A ── B | A → B |
| Meaning | "A and B are connected" | "A points to B (one-way)" |
| Real-world example | Facebook friendship (mutual) | Instagram follow (one-way) |
| Edge (A,B) means | Can go A→B AND B→A | Can ONLY go A→B |

### Sparse vs Dense Graph

| Type | Definition | Edges | Best Representation |
|------|-----------|-------|---------------------|
| Sparse | Few edges relative to vertices | E << V² | Adjacency List |
| Dense | Many edges relative to vertices | E ≈ V² | Adjacency Matrix |

### Adjacency
> Two vertices are **adjacent** if there's an edge directly connecting them.
```
    A ─── B ─── C
    
A and B are adjacent (directly connected)
A and C are NOT adjacent (must go through B)
B and C are adjacent
```

### Self-Loop
> An edge that connects a vertex to ITSELF.
```
    A ─── B
    │     ↺   ← self-loop on B
    C
```

### Multigraph
> A graph that allows MULTIPLE edges between the same pair of vertices.

---

## 1.3 — Types of Graphs (Complete List)

| Type | Description | Example |
|------|-------------|---------|
| **Undirected** | Edges have no direction | Road between cities (2-way) |
| **Directed (Digraph)** | Edges have direction (arrows) | One-way streets |
| **Weighted** | Edges have costs/weights | Road map with distances |
| **Unweighted** | All edges are equal | Social network connections |
| **Connected** | Path exists between all vertex pairs | Single landmass road map |
| **Disconnected** | Some vertices can't reach others | Islands without bridges |
| **Cyclic** | Contains at least one cycle | Circular road |
| **Acyclic** | No cycles exist | Family tree |
| **DAG** | Directed Acyclic Graph | Task dependencies |
| **Complete (K_n)** | Every vertex connected to every other | Round-robin tournament |
| **Bipartite** | Vertices split into 2 groups, edges only between groups | Students-Courses |
| **Tree** | Connected acyclic graph with n-1 edges | File system hierarchy |

---

## 1.4 — Graph vs Tree vs Linked List

| Feature | Linked List | Tree | Graph |
|---------|-------------|------|-------|
| Structure | Linear chain | Hierarchical | Network |
| Connections | Each node → 1 next | Parent → children | Any vertex → any vertex |
| Cycles | No | No | Can have cycles |
| Root/Head | Has head | Has root | No special start |
| Traversal | Linear | BFS/DFS/Inorder etc. | BFS/DFS |
| Edges for n nodes | n-1 | n-1 | 0 to n(n-1)/2 |

> **Key Insight:** A linked list IS a special case of a tree, and a tree IS a special case of a graph. Every linked list is a tree, and every tree is a graph, but NOT vice versa!

---

> **🎯 End of Graph Foundations. You now understand: What graphs are, ALL terminology, types of graphs, and how they compare to trees and linked lists. NEXT → Graph Representations!**
