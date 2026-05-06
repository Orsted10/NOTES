# 📘 DFS (Depth-First Search) — Complete Deep Dive

---

## 4.1 — What is DFS?

### 🧠 Analogy: Exploring a Maze
Imagine you're in a maze. Your strategy:
1. Pick a path and go as **DEEP** as possible
2. When you hit a **dead end**, **backtrack** to the last junction
3. Try a different path from that junction
4. Repeat until you've explored everything

That's DFS! Go **deep first**, then backtrack.

### 📖 Formal Definition
> **DFS is a graph traversal algorithm that explores as far as possible along each branch before backtracking. Starting from a source vertex, it goes DEEP into one path, then backtracks to explore other paths. It uses a STACK (or recursion).**

### BFS vs DFS at a Glance
```
BFS (Breadth-First):                DFS (Depth-First):
Explores level by level             Explores path by path
Uses QUEUE (FIFO)                   Uses STACK (LIFO) or RECURSION
Finds shortest path                 Finds ANY path
Goes WIDE first                     Goes DEEP first

        0                                  0
       / \                                / \
      1   2        BFS: 0,1,2,3,4        1   2       DFS: 0,1,3,4,2
     / \                                / \
    3   4                              3   4
```

---

## 4.2 — How DFS Works (Step-by-Step Tracing)

### The Algorithm (English)
```
RECURSIVE VERSION:
1. Mark current vertex as visited
2. Process it (print it)
3. For each neighbor of current vertex:
   - If not visited: recursively call DFS on that neighbor
```

### Detailed Tracing

```
Graph:
    0 ─── 1 ─── 4
    │     │
    2 ─── 3

Adjacency List:
0: [1, 2]
1: [0, 3, 4]
2: [0, 3]
3: [1, 2]
4: [1]

Starting from vertex 0
```

```
CALL dfs(0):
  visited[0] = 1, print "0"
  Neighbors of 0: [1, 2]
  - 1 not visited → CALL dfs(1)
  
    CALL dfs(1):
      visited[1] = 1, print "1"
      Neighbors of 1: [0, 3, 4]
      - 0 already visited → SKIP
      - 3 not visited → CALL dfs(3)
      
        CALL dfs(3):
          visited[3] = 1, print "3"
          Neighbors of 3: [1, 2]
          - 1 already visited → SKIP
          - 2 not visited → CALL dfs(2)
          
            CALL dfs(2):
              visited[2] = 1, print "2"
              Neighbors of 2: [0, 3]
              - 0 already visited → SKIP
              - 3 already visited → SKIP
              No more neighbors → RETURN ← backtrack!
          
          No more neighbors for 3 → RETURN ← backtrack!
      
      Back in dfs(1):
      - 4 not visited → CALL dfs(4)
      
        CALL dfs(4):
          visited[4] = 1, print "4"
          Neighbors of 4: [1]
          - 1 already visited → SKIP
          No more neighbors → RETURN ← backtrack!
      
      No more neighbors for 1 → RETURN ← backtrack!
  
  Back in dfs(0):
  - 2 already visited → SKIP
  No more neighbors → RETURN ← DONE!

FINAL DFS ORDER: 0 → 1 → 3 → 2 → 4
```

**Notice:** DFS went DEEP — 0→1→3→2 — before backtracking to explore 4.

---

## 4.3 — DFS Code (Recursive) Using Adjacency Matrix

```c
#include <stdio.h>

#define MAX 100

// ============================================
// GLOBAL: visited array
// PURPOSE: Track which vertices have been visited
// WHY GLOBAL: Because DFS is recursive, and we need ALL recursive calls
//             to share the SAME visited array. If it were local, each
//             call would have its own copy and wouldn't know what others visited.
// ============================================
int visited[MAX] = {0};

// ============================================
// FUNCTION: dfs (Recursive)
// PURPOSE:  Perform DFS traversal from a given vertex
// PARAMS:   adjMatrix = the adjacency matrix
//           vertices  = total number of vertices
//           current   = the vertex we're currently visiting
//
// HOW IT WORKS:
//   1. Mark current as visited (so we don't revisit it)
//   2. Print it (processing step)
//   3. For each possible neighbor (check all columns in the row):
//      - If edge exists AND neighbor not visited → recurse on neighbor
//   4. When all neighbors are done, the function returns (BACKTRACK)
//
// WHY RECURSION WORKS:
//   Recursion naturally uses the CALL STACK as a stack data structure.
//   When we call dfs(neighbor), the current function is "paused" and
//   pushed onto the stack. When the recursive call finishes, we come
//   back to where we left off — this IS backtracking!
// ============================================
void dfs(int adjMatrix[MAX][MAX], int vertices, int current) {
    // STEP 1: Mark this vertex as visited
    visited[current] = 1;
    // WHY: Without this, we might visit this vertex again through a different path,
    // causing an infinite loop (especially in graphs with cycles!)
    
    // STEP 2: Process the vertex (print it)
    printf("%d ", current);
    
    // STEP 3: Explore all neighbors
    for (int i = 0; i < vertices; i++) {
        // Check TWO conditions:
        // 1. adjMatrix[current][i] == 1 → there IS an edge from current to i
        // 2. visited[i] == 0 → vertex i has NOT been visited yet
        if (adjMatrix[current][i] == 1 && visited[i] == 0) {
            // RECURSE: Go deeper! Visit vertex i next.
            // This pauses the current function and starts a new one for vertex i.
            // When vertex i and all ITS descendants are done, we come back here
            // and continue the for loop to check the next neighbor.
            dfs(adjMatrix, vertices, i);
        }
    }
    // When the for loop ends, ALL neighbors (and their sub-graphs) have been
    // explored. The function returns, and we BACKTRACK to the calling function.
}

int main() {
    int adjMatrix[MAX][MAX] = {0};
    int vertices = 5;
    
    // Build graph:
    //     0 --- 1 --- 4
    //     |     |
    //     2 --- 3
    adjMatrix[0][1] = adjMatrix[1][0] = 1;
    adjMatrix[0][2] = adjMatrix[2][0] = 1;
    adjMatrix[1][3] = adjMatrix[3][1] = 1;
    adjMatrix[1][4] = adjMatrix[4][1] = 1;
    adjMatrix[2][3] = adjMatrix[3][2] = 1;
    
    printf("DFS Traversal: ");
    dfs(adjMatrix, vertices, 0);
    printf("\n");
    // Output: DFS Traversal: 0 1 3 2 4
    
    return 0;
}
```

---

## 4.4 — DFS Code (Iterative with Explicit Stack)

```c
#include <stdio.h>

#define MAX 100

// ============================================
// Iterative DFS uses an EXPLICIT stack instead of recursion.
// WHY? Some graphs are very deep, and recursion might cause
// STACK OVERFLOW (too many function calls). An explicit stack avoids this.
// ============================================

int stack[MAX];
int top = -1;

void push(int v) { stack[++top] = v; }
int pop() { return stack[top--]; }
int isStackEmpty() { return top == -1; }

void dfsIterative(int adjMatrix[MAX][MAX], int vertices, int start) {
    int visited[MAX] = {0};
    
    // Push starting vertex onto stack
    push(start);
    
    printf("DFS (Iterative): ");
    
    while (!isStackEmpty()) {
        // Pop a vertex from stack
        int current = pop();
        
        // If already visited, skip it
        // WHY: Unlike BFS where we mark visited when ENQUEUING,
        // in iterative DFS a vertex might be pushed multiple times
        // before being processed. We check here to avoid duplicates.
        if (visited[current]) continue;
        
        // Mark as visited and process
        visited[current] = 1;
        printf("%d ", current);
        
        // Push ALL unvisited neighbors onto the stack
        // We go in REVERSE order so that the smallest-numbered
        // neighbor is on TOP of the stack (processed first)
        for (int i = vertices - 1; i >= 0; i--) {
            if (adjMatrix[current][i] == 1 && !visited[i]) {
                push(i);
            }
        }
    }
    printf("\n");
}

int main() {
    int adjMatrix[MAX][MAX] = {0};
    int vertices = 5;
    
    adjMatrix[0][1] = adjMatrix[1][0] = 1;
    adjMatrix[0][2] = adjMatrix[2][0] = 1;
    adjMatrix[1][3] = adjMatrix[3][1] = 1;
    adjMatrix[1][4] = adjMatrix[4][1] = 1;
    adjMatrix[2][3] = adjMatrix[3][2] = 1;
    
    dfsIterative(adjMatrix, vertices, 0);
    // Output: DFS (Iterative): 0 1 3 2 4
    
    return 0;
}
```

---

## 4.5 — DFS Using Adjacency List (Recursive)

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int dest;
    struct Node *next;
} Node;

typedef struct Graph {
    int numVertices;
    Node **adjLists;
} Graph;

Node* createNode(int d) { Node *n=(Node*)malloc(sizeof(Node)); n->dest=d; n->next=NULL; return n; }

Graph* createGraph(int v) {
    Graph *g=(Graph*)malloc(sizeof(Graph));
    g->numVertices=v;
    g->adjLists=(Node**)malloc(v*sizeof(Node*));
    for(int i=0;i<v;i++) g->adjLists[i]=NULL;
    return g;
}

void addEdge(Graph *g, int s, int d) {
    Node *n=createNode(d); n->next=g->adjLists[s]; g->adjLists[s]=n;
    n=createNode(s); n->next=g->adjLists[d]; g->adjLists[d]=n;
}

int visited[100] = {0};

// ============================================
// DFS with Adjacency List
// KEY DIFFERENCE: Instead of checking all V columns,
// we only traverse the linked list of actual neighbors.
// This makes it O(V + E) total instead of O(V²).
// ============================================
void dfs(Graph *g, int current) {
    visited[current] = 1;
    printf("%d ", current);
    
    // Traverse ONLY actual neighbors (linked list)
    Node *temp = g->adjLists[current];
    while (temp != NULL) {
        if (!visited[temp->dest]) {
            dfs(g, temp->dest);
        }
        temp = temp->next;
    }
}

int main() {
    Graph *g = createGraph(6);
    addEdge(g, 0, 1); addEdge(g, 0, 2);
    addEdge(g, 1, 3); addEdge(g, 2, 4);
    addEdge(g, 3, 5); addEdge(g, 4, 5);
    
    printf("DFS: ");
    dfs(g, 0);
    printf("\n");
    return 0;
}
```

---

## 4.6 — DFS vs BFS Comparison

| Feature | BFS | DFS |
|---------|-----|-----|
| Data Structure | Queue (FIFO) | Stack/Recursion (LIFO) |
| Exploration | Level by level (wide) | Path by path (deep) |
| Shortest Path? | ✅ YES (unweighted) | ❌ NO |
| Time Complexity | O(V + E) | O(V + E) |
| Space Complexity | O(V) | O(V) (O(height) for recursion) |
| Complete? | Yes (finds all reachable) | Yes |
| Use When | Shortest path, level order | Cycle detection, topological sort, maze solving |
| Memory Usage | Can be high (wide graphs) | Lower for deep graphs |
| Backtracking | No | Yes (natural backtracking) |

---

## 4.7 — DFS Applications

| Application | How DFS Helps |
|-------------|---------------|
| **Cycle detection** | If we revisit a vertex during DFS, there's a cycle |
| **Topological sorting** | Linear ordering of DAG vertices (tasks with dependencies) |
| **Connected components** | Run DFS from each unvisited vertex |
| **Path finding** | Find if path exists between two vertices |
| **Maze solving** | DFS naturally explores one path fully before backtracking |
| **Strongly connected components** | Kosaraju's/Tarjan's algorithm uses DFS |
| **Solving puzzles** | Sudoku, N-Queens — DFS with backtracking |

---

> **🎯 End of DFS. You now deeply understand the concept, recursion vs iteration, tracing, both matrix and list implementations, and BFS vs DFS comparison. NEXT → Theory Questions!**
