# 📘 GRAPH REPRESENTATIONS — Adjacency Matrix & Adjacency List

---

## 2.1 — How Do We Store a Graph in a Computer?

A graph on paper is just circles and lines. But a computer needs **data structures** to store it. Two main ways:

1. **Adjacency Matrix** — A 2D array (table)
2. **Adjacency List** — An array of linked lists

We'll learn BOTH in extreme detail.

---

## 2.2 — ADJACENCY MATRIX

### Concept
Create a **2D array** of size V×V (V = number of vertices). If there's an edge between vertex `i` and vertex `j`, set `matrix[i][j] = 1`. Otherwise, `0`.

### Example Graph
```
    0 ─── 1
    │     │
    2 ─── 3

Vertices: 0, 1, 2, 3 (4 vertices)
Edges: (0,1), (0,2), (1,3), (2,3)
```

### The Matrix
```
        0    1    2    3       ← Column = destination vertex
    ┌────┬────┬────┬────┐
  0 │  0 │  1 │  1 │  0 │     Row 0: vertex 0 connects to 1 and 2
    ├────┼────┼────┼────┤
  1 │  1 │  0 │  0 │  1 │     Row 1: vertex 1 connects to 0 and 3
    ├────┼────┼────┼────┤
  2 │  1 │  0 │  0 │  1 │     Row 2: vertex 2 connects to 0 and 3
    ├────┼────┼────┼────┤
  3 │  0 │  1 │  1 │  0 │     Row 3: vertex 3 connects to 1 and 2
    └────┴────┴────┴────┘
↑ Row = source vertex

matrix[0][1] = 1 means "edge exists from vertex 0 to vertex 1"
matrix[0][3] = 0 means "NO edge from vertex 0 to vertex 3"
```

### Key Properties of Adjacency Matrix
1. **For undirected graphs:** The matrix is SYMMETRIC — `matrix[i][j] = matrix[j][i]` always (because if A connects to B, B connects to A)
2. **Diagonal is 0** (no self-loops): `matrix[i][i] = 0`
3. **For directed graphs:** Matrix may NOT be symmetric — `matrix[i][j]` can differ from `matrix[j][i]`
4. **For weighted graphs:** Instead of 1, store the weight. Use 0 or ∞ for no edge.

### Complete C Code — Adjacency Matrix

```c
#include <stdio.h>
#include <stdlib.h>

// We define MAX_VERTICES as a constant.
// This limits how big our graph can be.
// In adjacency matrix, we need a V×V array, so we declare it with a max size.
#define MAX_VERTICES 100

// The graph structure:
// - adjMatrix[i][j] stores 1 if edge exists between i and j, 0 otherwise
// - numVertices stores how many vertices the graph actually has
typedef struct Graph {
    int adjMatrix[MAX_VERTICES][MAX_VERTICES];
    int numVertices;
} Graph;

// ============================================
// FUNCTION: initGraph
// PURPOSE:  Initialize the graph — set all edges to 0 (no connections)
// WHY:      Without initialization, the array may contain GARBAGE values
//           from whatever was in that memory before. We need clean 0s.
// PARAMS:   g = pointer to Graph struct, vertices = how many vertices
// ============================================
void initGraph(Graph *g, int vertices) {
    g->numVertices = vertices;
    
    // Set EVERY cell to 0 — "no edge exists"
    // We use two nested loops to cover all rows and columns
    for (int i = 0; i < vertices; i++) {
        for (int j = 0; j < vertices; j++) {
            g->adjMatrix[i][j] = 0;
        }
    }
    // After this, the matrix is a V×V grid of all zeros
}

// ============================================
// FUNCTION: addEdge
// PURPOSE:  Add an edge between vertex src and vertex dest
// WHY:      This is how we "draw" a connection between two vertices
// HOW:      Set matrix[src][dest] = 1 AND matrix[dest][src] = 1
//           (both directions because it's undirected)
// PARAMS:   g = pointer to Graph, src = source vertex, dest = destination vertex
// ============================================
void addEdge(Graph *g, int src, int dest) {
    // Validate: make sure src and dest are valid vertex numbers
    if (src < 0 || src >= g->numVertices || dest < 0 || dest >= g->numVertices) {
        printf("Error: Invalid vertices %d or %d!\n", src, dest);
        return;
    }
    
    g->adjMatrix[src][dest] = 1;   // Edge from src to dest
    g->adjMatrix[dest][src] = 1;   // Edge from dest to src (UNDIRECTED, so both ways)
    
    // For DIRECTED graph, you would ONLY do:
    // g->adjMatrix[src][dest] = 1;
    // (one direction only)
}

// ============================================
// FUNCTION: removeEdge
// PURPOSE:  Remove an edge between two vertices
// HOW:      Set both matrix entries back to 0
// ============================================
void removeEdge(Graph *g, int src, int dest) {
    g->adjMatrix[src][dest] = 0;
    g->adjMatrix[dest][src] = 0;
}

// ============================================
// FUNCTION: hasEdge
// PURPOSE:  Check if an edge exists between two vertices
// RETURNS:  1 if edge exists, 0 if not
// HOW:      Just read the matrix value at [src][dest]
// ============================================
int hasEdge(Graph *g, int src, int dest) {
    return g->adjMatrix[src][dest];
}

// ============================================
// FUNCTION: printMatrix
// PURPOSE:  Display the entire adjacency matrix in a readable format
// ============================================
void printMatrix(Graph *g) {
    int v = g->numVertices;
    
    // Print column header
    printf("    ");
    for (int i = 0; i < v; i++) printf("%3d", i);
    printf("\n    ");
    for (int i = 0; i < v; i++) printf("---");
    printf("\n");
    
    // Print each row
    for (int i = 0; i < v; i++) {
        printf("%2d | ", i);  // Row label
        for (int j = 0; j < v; j++) {
            printf("%2d ", g->adjMatrix[i][j]);
        }
        printf("\n");
    }
}

// ============================================
// FUNCTION: printAdjacent
// PURPOSE:  Print all vertices adjacent to a given vertex
// HOW:      Scan the entire row for that vertex — wherever there's a 1,
//           that column number is an adjacent vertex
// ============================================
void printAdjacent(Graph *g, int vertex) {
    printf("Vertices adjacent to %d: ", vertex);
    for (int i = 0; i < g->numVertices; i++) {
        if (g->adjMatrix[vertex][i] == 1) {
            printf("%d ", i);
        }
    }
    printf("\n");
}

// ============================================
// FUNCTION: getDegree
// PURPOSE:  Get the degree (number of connections) of a vertex
// HOW:      Count all 1s in that vertex's row
// ============================================
int getDegree(Graph *g, int vertex) {
    int degree = 0;
    for (int i = 0; i < g->numVertices; i++) {
        if (g->adjMatrix[vertex][i] == 1) {
            degree++;
        }
    }
    return degree;
}

int main() {
    Graph g;
    initGraph(&g, 5);  // Create graph with 5 vertices (0,1,2,3,4)
    
    // Build this graph:
    //     0 --- 1
    //     |   / |
    //     |  /  |
    //     | /   |
    //     2 --- 3
    //           |
    //           4
    addEdge(&g, 0, 1);
    addEdge(&g, 0, 2);
    addEdge(&g, 1, 2);
    addEdge(&g, 1, 3);
    addEdge(&g, 2, 3);
    addEdge(&g, 3, 4);
    
    printf("=== Adjacency Matrix ===\n");
    printMatrix(&g);
    
    printf("\n");
    printAdjacent(&g, 1);   // Vertices adjacent to 1: 0 2 3
    printf("Degree of vertex 1: %d\n", getDegree(&g, 1));  // 3
    
    printf("Edge between 0 and 3? %s\n", hasEdge(&g, 0, 3) ? "YES" : "NO");  // NO
    printf("Edge between 0 and 1? %s\n", hasEdge(&g, 0, 1) ? "YES" : "NO");  // YES
    
    return 0;
}
```

### Adjacency Matrix for DIRECTED Graph
```c
// Only change: addEdge sets ONE direction only
void addEdgeDirected(Graph *g, int src, int dest) {
    g->adjMatrix[src][dest] = 1;  // ONLY src → dest, NOT dest → src
}

// Example: Edge A→B exists but B→A does NOT
```

### Adjacency Matrix for WEIGHTED Graph
```c
// Instead of 1/0, store the weight. Use 0 or -1 or INT_MAX for "no edge"
void addWeightedEdge(Graph *g, int src, int dest, int weight) {
    g->adjMatrix[src][dest] = weight;
    g->adjMatrix[dest][src] = weight;  // remove for directed
}
```

### Pros & Cons of Adjacency Matrix

| Pros ✅ | Cons ❌ |
|---------|---------|
| Simple to implement | Wastes memory for sparse graphs: O(V²) always |
| Check if edge exists = O(1) — just read matrix[i][j] | Adding a vertex = create new bigger matrix = O(V²) |
| Good for dense graphs | Iterating over all neighbors = O(V) even if vertex has few edges |

---

## 2.3 — ADJACENCY LIST

### Concept
Instead of a 2D array, use an **array of linked lists**. Each vertex gets its own linked list that stores all the vertices it's connected to.

### Example (Same Graph)
```
    0 ─── 1
    │     │
    2 ─── 3

Adjacency List:
Vertex 0: → [1] → [2] → NULL
Vertex 1: → [0] → [3] → NULL
Vertex 2: → [0] → [3] → NULL
Vertex 3: → [1] → [2] → NULL
```

Each row is a linked list of "neighbors."

### Complete C Code — Adjacency List

```c
#include <stdio.h>
#include <stdlib.h>

// ============================================
// STRUCTURE: AdjListNode
// PURPOSE:   One node in the adjacency linked list
// FIELDS:    dest = the vertex number this edge leads to
//            next = pointer to the next neighbor in the list
// WHY:       Each vertex has a list of neighbors. Each neighbor is a node.
// ============================================
typedef struct AdjListNode {
    int dest;                    // Destination vertex
    struct AdjListNode *next;    // Pointer to next neighbor
} AdjListNode;

// ============================================
// STRUCTURE: AdjList
// PURPOSE:   The head of one adjacency list (for one vertex)
// FIELDS:    head = pointer to the first neighbor node
// WHY:       Each vertex needs a "starting point" for its neighbor list
// ============================================
typedef struct AdjList {
    AdjListNode *head;           // Head of linked list of neighbors
} AdjList;

// ============================================
// STRUCTURE: Graph
// PURPOSE:   The complete graph containing all adjacency lists
// FIELDS:    numVertices = total number of vertices
//            array = array of AdjList, one per vertex
// WHY:       We need one linked list PER vertex. This array holds them all.
// ============================================
typedef struct Graph {
    int numVertices;
    AdjList *array;              // Dynamic array of adjacency lists
} Graph;

// ============================================
// FUNCTION: createNode
// PURPOSE:  Create a new adjacency list node
// PARAMS:   dest = the destination vertex number
// RETURNS:  Pointer to the new node
// WHY:      Every time we add an edge, we create a node to represent
//           that connection in the linked list
// ============================================
AdjListNode* createNode(int dest) {
    // Step 1: Allocate memory for one node
    AdjListNode *newNode = (AdjListNode *)malloc(sizeof(AdjListNode));
    
    // Step 2: Set the destination vertex
    newNode->dest = dest;
    
    // Step 3: Initialize next to NULL
    newNode->next = NULL;
    
    // Step 4: Return the node
    return newNode;
}

// ============================================
// FUNCTION: createGraph
// PURPOSE:  Create and initialize an empty graph with V vertices
// HOW:      1. Allocate memory for the Graph struct
//           2. Set numVertices
//           3. Allocate an array of V AdjList heads
//           4. Set each head to NULL (no neighbors yet)
// RETURNS:  Pointer to the new Graph
// ============================================
Graph* createGraph(int vertices) {
    // Step 1: Allocate the graph structure itself
    Graph *graph = (Graph *)malloc(sizeof(Graph));
    graph->numVertices = vertices;
    
    // Step 2: Allocate array of adjacency lists (one per vertex)
    // This creates an array like: [list0, list1, list2, ..., list(V-1)]
    graph->array = (AdjList *)malloc(vertices * sizeof(AdjList));
    
    // Step 3: Initialize each list as empty (head = NULL)
    // An empty list means "this vertex has no neighbors yet"
    for (int i = 0; i < vertices; i++) {
        graph->array[i].head = NULL;
    }
    
    return graph;
}

// ============================================
// FUNCTION: addEdge (UNDIRECTED)
// PURPOSE:  Add an edge between src and dest
// HOW:      We add dest to src's list AND src to dest's list
//           (because undirected = both directions)
//           We INSERT AT BEGINNING of each list (O(1) — fastest!)
// ============================================
void addEdge(Graph *graph, int src, int dest) {
    // --- Add dest to src's neighbor list ---
    // Step 1: Create a node for destination vertex
    AdjListNode *newNode = createNode(dest);
    
    // Step 2: Insert at BEGINNING of src's linked list
    //         Why beginning? Because it's O(1)! No traversal needed.
    //         The new node's next points to whatever was the old head
    newNode->next = graph->array[src].head;
    
    // Step 3: Update the head to point to new node
    graph->array[src].head = newNode;
    
    // --- Add src to dest's neighbor list (UNDIRECTED) ---
    // Same steps, but reversed: add src to dest's list
    newNode = createNode(src);
    newNode->next = graph->array[dest].head;
    graph->array[dest].head = newNode;
    
    // For DIRECTED graph: remove the second block above
}

// ============================================
// FUNCTION: printGraph
// PURPOSE:  Display the entire adjacency list representation
// HOW:      For each vertex, traverse its linked list and print all neighbors
// ============================================
void printGraph(Graph *graph) {
    for (int i = 0; i < graph->numVertices; i++) {
        printf("Vertex %d:", i);
        
        // Traverse the linked list for vertex i
        AdjListNode *current = graph->array[i].head;
        while (current != NULL) {
            printf(" → %d", current->dest);
            current = current->next;
        }
        printf(" → NULL\n");
    }
}

// ============================================
// FUNCTION: hasEdge
// PURPOSE:  Check if edge exists from src to dest
// HOW:      Traverse src's neighbor list and look for dest
// ============================================
int hasEdge(Graph *graph, int src, int dest) {
    AdjListNode *current = graph->array[src].head;
    while (current != NULL) {
        if (current->dest == dest) return 1;  // Found!
        current = current->next;
    }
    return 0;  // Not found
}

// ============================================
// FUNCTION: getDegree
// PURPOSE:  Count how many neighbors a vertex has
// HOW:      Count nodes in the vertex's linked list
// ============================================
int getDegree(Graph *graph, int vertex) {
    int degree = 0;
    AdjListNode *current = graph->array[vertex].head;
    while (current != NULL) {
        degree++;
        current = current->next;
    }
    return degree;
}

// ============================================
// FUNCTION: freeGraph
// PURPOSE:  Free ALL memory used by the graph
// WHY:      Each vertex's linked list has dynamically allocated nodes.
//           We must free EVERY node to prevent memory leaks.
// ============================================
void freeGraph(Graph *graph) {
    for (int i = 0; i < graph->numVertices; i++) {
        AdjListNode *current = graph->array[i].head;
        while (current != NULL) {
            AdjListNode *temp = current;
            current = current->next;
            free(temp);
        }
    }
    free(graph->array);  // Free the array of lists
    free(graph);         // Free the graph struct itself
}

int main() {
    Graph *graph = createGraph(5);
    
    addEdge(graph, 0, 1);
    addEdge(graph, 0, 2);
    addEdge(graph, 1, 2);
    addEdge(graph, 1, 3);
    addEdge(graph, 2, 3);
    addEdge(graph, 3, 4);
    
    printf("=== Adjacency List ===\n");
    printGraph(graph);
    
    printf("\nEdge 0-3? %s\n", hasEdge(graph, 0, 3) ? "YES" : "NO");
    printf("Edge 1-3? %s\n", hasEdge(graph, 1, 3) ? "YES" : "NO");
    printf("Degree of vertex 2: %d\n", getDegree(graph, 2));
    
    freeGraph(graph);
    return 0;
}
```

**Output:**
```
=== Adjacency List ===
Vertex 0: → 2 → 1 → NULL
Vertex 1: → 3 → 2 → 0 → NULL
Vertex 2: → 3 → 1 → 0 → NULL
Vertex 3: → 4 → 2 → 1 → NULL
Vertex 4: → 3 → NULL

Edge 0-3? NO
Edge 1-3? YES
Degree of vertex 2: 3
```

(Note: Order of neighbors may be reversed because we insert at beginning)

### Pros & Cons of Adjacency List

| Pros ✅ | Cons ❌ |
|---------|---------|
| Memory efficient for sparse graphs: O(V + E) | Checking if edge exists = O(degree) not O(1) |
| Iterating over neighbors = O(degree) only | Slightly more complex to implement |
| Easy to add vertices | Harder to remove edges |

---

## 2.4 — Matrix vs List: WHEN TO USE WHICH

| Criterion | Adjacency Matrix | Adjacency List |
|-----------|:-:|:-:|
| Space | O(V²) | O(V + E) |
| Check edge exists | O(1) ✅ | O(V) worst case |
| Find all neighbors | O(V) | O(degree) ✅ |
| Add edge | O(1) ✅ | O(1) ✅ |
| Remove edge | O(1) ✅ | O(degree) |
| Dense graph (many edges) | ✅ Better | ❌ Wasteful overhead |
| Sparse graph (few edges) | ❌ Wastes space | ✅ Better |
| BFS/DFS traversal | O(V²) | O(V + E) ✅ |

**Rule of thumb:**
- **Dense graph (E ≈ V²)** → Adjacency Matrix
- **Sparse graph (E << V²)** → Adjacency List
- **Most real-world graphs are sparse** → Adjacency List is usually preferred

---

> **🎯 End of Graph Representations. You now deeply understand BOTH representations with full code, when to use each, and their tradeoffs. NEXT → BFS Traversal!**
