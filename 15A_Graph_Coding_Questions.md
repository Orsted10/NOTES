# 📘 GRAPH CODING QUESTIONS — Part A (Problems 1–15)

> Every solution: Problem → Approach → Full Code → Every line explained → Output

---

## PROBLEM 1: Create a Graph Using Adjacency Matrix and Display It

**Problem:** Take number of vertices and edges as input, build the graph, display the matrix.

```c
#include <stdio.h>

#define MAX 100

int main() {
    int adjMatrix[MAX][MAX] = {0};
    // Initialize entire matrix to 0 — "no edges exist yet"
    // {0} sets the first element to 0, and C automatically zeros the rest
    
    int vertices, edges;
    
    printf("Enter number of vertices: ");
    scanf("%d", &vertices);
    // How many nodes in our graph? This determines the matrix size (V × V)
    
    printf("Enter number of edges: ");
    scanf("%d", &edges);
    // How many connections? We'll ask for each one below
    
    // Read each edge: two vertex numbers that are connected
    for (int i = 0; i < edges; i++) {
        int u, v;
        printf("Enter edge %d (u v): ", i + 1);
        scanf("%d %d", &u, &v);
        // u and v are the two vertices connected by this edge
        
        adjMatrix[u][v] = 1;   // Mark edge from u to v
        adjMatrix[v][u] = 1;   // Mark edge from v to u (UNDIRECTED — both ways)
        // For DIRECTED graph, only do: adjMatrix[u][v] = 1;
    }
    
    // Display the adjacency matrix
    printf("\nAdjacency Matrix:\n");
    printf("    ");
    for (int i = 0; i < vertices; i++) printf("%3d", i);
    printf("\n");
    
    for (int i = 0; i < vertices; i++) {
        printf("%3d:", i);
        for (int j = 0; j < vertices; j++) {
            printf("%3d", adjMatrix[i][j]);
        }
        printf("\n");
    }
    
    return 0;
}
```

**Sample Output:**
```
Enter number of vertices: 4
Enter number of edges: 4
Enter edge 1 (u v): 0 1
Enter edge 2 (u v): 0 2
Enter edge 3 (u v): 1 3
Enter edge 4 (u v): 2 3

Adjacency Matrix:
      0  1  2  3
  0:  0  1  1  0
  1:  1  0  0  1
  2:  1  0  0  1
  3:  0  1  1  0
```

---

## PROBLEM 2: Create a Graph Using Adjacency List and Display It

```c
#include <stdio.h>
#include <stdlib.h>

// Each node in the adjacency list stores a neighbor's vertex number
typedef struct Node {
    int vertex;           // Which vertex this neighbor is
    struct Node *next;    // Next neighbor in the list
} Node;

// Create a new adjacency list node
// WHY: Every time we add an edge, we need a new node in the linked list
Node* createNode(int v) {
    Node *newNode = (Node *)malloc(sizeof(Node));
    newNode->vertex = v;
    newNode->next = NULL;
    return newNode;
}

#define MAX 100
Node *adjList[MAX];  // Array of linked list heads — one per vertex

int main() {
    int vertices, edges;
    
    printf("Enter vertices and edges: ");
    scanf("%d %d", &vertices, &edges);
    
    // Initialize all lists to empty (NULL = no neighbors)
    for (int i = 0; i < vertices; i++)
        adjList[i] = NULL;
    
    // Read edges and build adjacency lists
    for (int i = 0; i < edges; i++) {
        int u, v;
        printf("Edge %d (u v): ", i + 1);
        scanf("%d %d", &u, &v);
        
        // Add v to u's list (INSERT AT BEGINNING — O(1))
        // Step 1: Create node for vertex v
        // Step 2: Make it point to current head of u's list
        // Step 3: Update u's head to this new node
        Node *n = createNode(v);
        n->next = adjList[u];       // new node → old head
        adjList[u] = n;              // head = new node
        
        // Add u to v's list (UNDIRECTED — both directions)
        n = createNode(u);
        n->next = adjList[v];
        adjList[v] = n;
    }
    
    // Display the adjacency list
    printf("\nAdjacency List:\n");
    for (int i = 0; i < vertices; i++) {
        printf("Vertex %d:", i);
        Node *temp = adjList[i];
        while (temp) {
            printf(" -> %d", temp->vertex);
            temp = temp->next;
        }
        printf(" -> NULL\n");
    }
    
    return 0;
}
```

---

## PROBLEM 3: BFS Traversal (Adjacency Matrix — User Input)

```c
#include <stdio.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX] = {0};
int queue[MAX], front = -1, rear = -1;

void enqueue(int v) { if(front==-1)front=0; queue[++rear]=v; }
int dequeue() { int v=queue[front++]; if(front>rear){front=-1;rear=-1;} return v; }
int isEmpty() { return front==-1; }

// BFS: Visit all vertices reachable from 'start', level by level
void bfs(int start, int n) {
    visited[start] = 1;    // Mark start as visited
    enqueue(start);         // Add start to queue
    
    printf("BFS from vertex %d: ", start);
    
    while (!isEmpty()) {
        int curr = dequeue();      // Get next vertex to process
        printf("%d ", curr);        // Process it (print)
        
        // Check all possible neighbors
        for (int i = 0; i < n; i++) {
            // adj[curr][i] == 1: edge exists from curr to i
            // !visited[i]: vertex i hasn't been visited yet
            if (adj[curr][i] == 1 && !visited[i]) {
                visited[i] = 1;    // Mark visited BEFORE enqueuing
                enqueue(i);         // Add to queue for later processing
            }
        }
    }
    printf("\n");
}

int main() {
    int n, edges, u, v, start;
    
    printf("Vertices: "); scanf("%d", &n);
    printf("Edges: "); scanf("%d", &edges);
    
    for (int i = 0; i < edges; i++) {
        printf("Edge (u v): "); scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    printf("Start vertex: "); scanf("%d", &start);
    bfs(start, n);
    
    return 0;
}
```

---

## PROBLEM 4: DFS Traversal (Adjacency Matrix — User Input)

```c
#include <stdio.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX] = {0};
int n;  // number of vertices (global so DFS recursive calls can access it)

// DFS: Go as deep as possible, then backtrack
// This function uses RECURSION — the call stack acts as our stack
void dfs(int vertex) {
    visited[vertex] = 1;           // Mark this vertex as visited
    printf("%d ", vertex);          // Process (print) it
    
    // Try every possible neighbor
    for (int i = 0; i < n; i++) {
        // If edge exists AND neighbor not visited
        if (adj[vertex][i] == 1 && !visited[i]) {
            dfs(i);                 // RECURSE — go deeper!
            // When dfs(i) returns, we BACKTRACK here and try the next neighbor
        }
    }
    // When all neighbors are explored, function returns (backtrack to caller)
}

int main() {
    int edges, u, v, start;
    
    printf("Vertices: "); scanf("%d", &n);
    printf("Edges: "); scanf("%d", &edges);
    
    for (int i = 0; i < edges; i++) {
        printf("Edge (u v): "); scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    printf("Start vertex: "); scanf("%d", &start);
    printf("DFS from vertex %d: ", start);
    dfs(start);
    printf("\n");
    
    return 0;
}
```

---

## PROBLEM 5: BFS and DFS Both — Complete Program

```c
#include <stdio.h>
#include <string.h>  // for memset — to reset arrays
#define MAX 100

int adj[MAX][MAX];
int visited[MAX];
int n;

// --- Queue for BFS ---
int queue[MAX], front, rear;
void initQueue() { front = rear = -1; }
void enqueue(int v) { if(front==-1)front=0; queue[++rear]=v; }
int dequeue() { int v=queue[front++]; if(front>rear){front=-1;rear=-1;} return v; }
int qEmpty() { return front==-1; }

void bfs(int start) {
    memset(visited, 0, sizeof(visited));
    // memset sets ALL bytes in visited[] to 0
    // WHY: Reset visited array so BFS starts fresh
    
    initQueue();
    visited[start] = 1;
    enqueue(start);
    
    printf("BFS: ");
    while (!qEmpty()) {
        int c = dequeue();
        printf("%d ", c);
        for (int i = 0; i < n; i++)
            if (adj[c][i] && !visited[i]) { visited[i]=1; enqueue(i); }
    }
    printf("\n");
}

void dfs(int v) {
    visited[v] = 1;
    printf("%d ", v);
    for (int i = 0; i < n; i++)
        if (adj[v][i] && !visited[i]) dfs(i);
}

int main() {
    int edges, u, v, start;
    
    printf("Vertices: "); scanf("%d", &n);
    printf("Edges: "); scanf("%d", &edges);
    
    memset(adj, 0, sizeof(adj));
    
    for (int i = 0; i < edges; i++) {
        printf("Edge (u v): "); scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    printf("Start vertex: "); scanf("%d", &start);
    
    bfs(start);
    
    memset(visited, 0, sizeof(visited)); // Reset for DFS
    printf("DFS: ");
    dfs(start);
    printf("\n");
    
    return 0;
}
```

---

## PROBLEM 6: Count Connected Components

**Problem:** Find how many disconnected groups exist in the graph.

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX];
int n;

// DFS to visit all vertices reachable from 'v'
void dfs(int v) {
    visited[v] = 1;
    for (int i = 0; i < n; i++)
        if (adj[v][i] && !visited[i]) dfs(i);
}

int main() {
    int edges, u, v;
    printf("Vertices: "); scanf("%d", &n);
    printf("Edges: "); scanf("%d", &edges);
    
    memset(adj, 0, sizeof(adj));
    memset(visited, 0, sizeof(visited));
    
    for (int i = 0; i < edges; i++) {
        printf("Edge: "); scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    int components = 0;
    
    // KEY LOGIC: Try DFS from every vertex.
    // If a vertex is unvisited, it starts a NEW component.
    // DFS will visit all vertices in that component.
    // Next unvisited vertex = new component.
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            // This vertex hasn't been reached by any previous DFS
            // So it's part of a NEW connected component
            printf("Component %d: ", components + 1);
            dfs(i);
            printf("\n");
            components++;
        }
    }
    
    printf("Total connected components: %d\n", components);
    return 0;
}
```

---

## PROBLEM 7: Detect Cycle in Undirected Graph (DFS)

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX];
int n;

// DFS with parent tracking
// parent = the vertex we came FROM (to avoid false cycle detection)
// WHY parent? In undirected graph, if A connects to B and B connects to A,
// going A→B then checking A from B would falsely detect a "cycle".
// We skip the parent to avoid this.
int dfsCycle(int vertex, int parent) {
    visited[vertex] = 1;
    
    for (int i = 0; i < n; i++) {
        if (adj[vertex][i]) {  // there's an edge
            if (!visited[i]) {
                // Neighbor not visited — continue DFS
                if (dfsCycle(i, vertex))
                    return 1;  // Cycle found deeper — propagate up
            }
            else if (i != parent) {
                // Neighbor IS visited AND it's NOT our parent
                // This means we've found a BACK EDGE → CYCLE!
                return 1;
            }
        }
    }
    return 0;  // No cycle found from this vertex
}

int main() {
    int edges, u, v;
    printf("Vertices: "); scanf("%d", &n);
    printf("Edges: "); scanf("%d", &edges);
    
    memset(adj, 0, sizeof(adj));
    memset(visited, 0, sizeof(visited));
    
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    int hasCycle = 0;
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            if (dfsCycle(i, -1)) {  // -1 = no parent (starting vertex)
                hasCycle = 1;
                break;
            }
        }
    }
    
    printf(hasCycle ? "Graph has a CYCLE!\n" : "Graph has NO cycle.\n");
    return 0;
}
```

---

## PROBLEM 8: Detect Cycle in Directed Graph (DFS)

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX], recStack[MAX];
int n;

// For directed graphs, we need a RECURSION STACK tracker
// recStack[v] = 1 means vertex v is currently in the DFS path
// If we encounter a vertex that's in recStack, we found a cycle
int dfsCycleDirected(int vertex) {
    visited[vertex] = 1;
    recStack[vertex] = 1;  // Add to current path
    
    for (int i = 0; i < n; i++) {
        if (adj[vertex][i]) {
            if (!visited[i]) {
                if (dfsCycleDirected(i)) return 1;
            }
            else if (recStack[i]) {
                // vertex i is in current recursion path → CYCLE!
                return 1;
            }
        }
    }
    
    recStack[vertex] = 0;  // Remove from current path (backtrack)
    return 0;
}

int main() {
    int edges, u, v;
    printf("Vertices: "); scanf("%d", &n);
    printf("Edges: "); scanf("%d", &edges);
    
    memset(adj, 0, sizeof(adj));
    memset(visited, 0, sizeof(visited));
    memset(recStack, 0, sizeof(recStack));
    
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = 1;  // DIRECTED — only one way
    }
    
    int hasCycle = 0;
    for (int i = 0; i < n; i++) {
        if (!visited[i] && dfsCycleDirected(i)) {
            hasCycle = 1; break;
        }
    }
    
    printf(hasCycle ? "Directed graph has CYCLE!\n" : "No cycle.\n");
    return 0;
}
```

---

## PROBLEM 9: Find Shortest Path Using BFS (Unweighted Graph)

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX];
int visited[MAX], dist[MAX], parent[MAX];
int queue[MAX], front=-1, rear=-1;

void enq(int v){if(front==-1)front=0; queue[++rear]=v;}
int deq(){int v=queue[front++]; if(front>rear){front=-1;rear=-1;} return v;}
int empty(){return front==-1;}

// BFS naturally finds shortest path in unweighted graphs
// dist[i] = shortest distance from source to vertex i
// parent[i] = which vertex comes before i in the shortest path
void bfsShortestPath(int n, int src) {
    memset(visited, 0, sizeof(visited));
    memset(dist, -1, sizeof(dist));     // -1 = unreachable
    memset(parent, -1, sizeof(parent)); // -1 = no parent
    
    visited[src] = 1;
    dist[src] = 0;      // Distance from source to itself = 0
    enq(src);
    
    while (!empty()) {
        int curr = deq();
        for (int i = 0; i < n; i++) {
            if (adj[curr][i] && !visited[i]) {
                visited[i] = 1;
                dist[i] = dist[curr] + 1;  // One more edge than current
                parent[i] = curr;            // Remember where we came from
                enq(i);
            }
        }
    }
}

// Print the path from source to destination by backtracking through parents
void printPath(int dest) {
    if (dest == -1) return;
    printPath(parent[dest]);   // Print path to parent first (recursion)
    printf("%d ", dest);        // Then print current vertex
}

int main() {
    int n, edges, u, v, src, dest;
    
    printf("Vertices, Edges: "); scanf("%d %d", &n, &edges);
    memset(adj, 0, sizeof(adj));
    
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    printf("Source, Destination: "); scanf("%d %d", &src, &dest);
    bfsShortestPath(n, src);
    
    if (dist[dest] == -1) {
        printf("No path exists!\n");
    } else {
        printf("Shortest distance: %d\n", dist[dest]);
        printf("Path: "); printPath(dest); printf("\n");
    }
    
    return 0;
}
```

---

## PROBLEM 10: Topological Sort Using DFS

**Problem:** Given a DAG, find a linear ordering where for every edge u→v, u comes before v.

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX];
int stack[MAX], top = -1;  // Stack to store topological order
int n;

// DFS-based topological sort:
// After ALL descendants of a vertex are processed, push it to stack.
// This ensures dependencies come before dependents.
void topoSort(int v) {
    visited[v] = 1;
    
    // First, visit ALL neighbors (descendants)
    for (int i = 0; i < n; i++) {
        if (adj[v][i] && !visited[i]) {
            topoSort(i);
        }
    }
    
    // AFTER all descendants are done, push this vertex
    // WHY AFTER? Because in topological order, a vertex must come
    // BEFORE all vertices it has edges to. By pushing AFTER descendants,
    // descendants end up lower in the stack, and this vertex ends up higher.
    stack[++top] = v;
}

int main() {
    int edges, u, v;
    printf("Vertices: "); scanf("%d", &n);
    printf("Edges: "); scanf("%d", &edges);
    
    memset(adj, 0, sizeof(adj));
    memset(visited, 0, sizeof(visited));
    
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = 1;  // Directed edge u → v
    }
    
    // Run topological sort from every unvisited vertex
    for (int i = 0; i < n; i++)
        if (!visited[i]) topoSort(i);
    
    // Print stack (top to bottom = topological order)
    printf("Topological Order: ");
    while (top >= 0)
        printf("%d ", stack[top--]);
    printf("\n");
    
    return 0;
}
```

---

## PROBLEM 11: Check if Graph is Bipartite (BFS 2-Coloring)

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], color[MAX];
int queue[MAX], front=-1, rear=-1;

void enq(int v){if(front==-1)front=0; queue[++rear]=v;}
int deq(){int v=queue[front++]; if(front>rear){front=-1;rear=-1;} return v;}
int empty(){return front==-1;}

// Try to 2-color the graph using BFS
// If we can, it's bipartite. If not, it's not.
int isBipartite(int n, int start) {
    memset(color, -1, sizeof(color));  // -1 = uncolored
    
    color[start] = 0;  // Color starting vertex with color 0
    enq(start);
    
    while (!empty()) {
        int curr = deq();
        
        for (int i = 0; i < n; i++) {
            if (adj[curr][i]) {
                if (color[i] == -1) {
                    // Uncolored neighbor — give OPPOSITE color
                    color[i] = 1 - color[curr];  // 0→1, 1→0
                    enq(i);
                }
                else if (color[i] == color[curr]) {
                    // Same color as current → CONFLICT → NOT bipartite!
                    return 0;
                }
            }
        }
    }
    return 1;  // No conflicts → bipartite
}

int main() {
    int n, edges, u, v;
    printf("Vertices, Edges: "); scanf("%d %d", &n, &edges);
    memset(adj, 0, sizeof(adj));
    
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    printf(isBipartite(n, 0) ? "Bipartite!\n" : "NOT Bipartite!\n");
    return 0;
}
```

---

## PROBLEM 12: Print All Paths from Source to Destination (DFS Backtracking)

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX];
int path[MAX], pathLen = 0;
int n;

// DFS with backtracking to find ALL paths
void printAllPaths(int src, int dest) {
    visited[src] = 1;
    path[pathLen++] = src;  // Add current vertex to path
    
    if (src == dest) {
        // Reached destination — print the current path
        for (int i = 0; i < pathLen; i++) {
            printf("%d", path[i]);
            if (i < pathLen - 1) printf(" -> ");
        }
        printf("\n");
    } else {
        // Not at destination yet — try all neighbors
        for (int i = 0; i < n; i++) {
            if (adj[src][i] && !visited[i]) {
                printAllPaths(i, dest);
            }
        }
    }
    
    // BACKTRACK: remove current vertex from path and unmark it
    // WHY: So that this vertex can be used in OTHER paths
    pathLen--;
    visited[src] = 0;
}

int main() {
    int edges, u, v, src, dest;
    printf("Vertices: "); scanf("%d", &n);
    printf("Edges: "); scanf("%d", &edges);
    
    memset(adj, 0, sizeof(adj));
    memset(visited, 0, sizeof(visited));
    
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    printf("Source, Destination: "); scanf("%d %d", &src, &dest);
    printf("All paths from %d to %d:\n", src, dest);
    printAllPaths(src, dest);
    
    return 0;
}
```

---

## PROBLEM 13: Degree of Each Vertex

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int main() {
    int adj[MAX][MAX] = {0};
    int n, edges, u, v;
    
    printf("Vertices, Edges: "); scanf("%d %d", &n, &edges);
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    // For each vertex, count how many 1s are in its row
    // That count IS the degree
    for (int i = 0; i < n; i++) {
        int degree = 0;
        for (int j = 0; j < n; j++) {
            if (adj[i][j] == 1) degree++;
        }
        printf("Degree of vertex %d = %d\n", i, degree);
    }
    
    return 0;
}
```

---

## PROBLEM 14: Check if Path Exists Between Two Vertices

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX];
int n;

// Simple DFS to check reachability
// Returns 1 if dest is reachable from src, 0 otherwise
int hasPath(int src, int dest) {
    if (src == dest) return 1;  // Already at destination!
    
    visited[src] = 1;
    
    for (int i = 0; i < n; i++) {
        if (adj[src][i] && !visited[i]) {
            if (hasPath(i, dest))  // If destination reachable from neighbor
                return 1;          // Then it's reachable from here too!
        }
    }
    
    return 0;  // Exhausted all paths — destination NOT reachable
}

int main() {
    int edges, u, v, src, dest;
    printf("Vertices: "); scanf("%d", &n);
    printf("Edges: "); scanf("%d", &edges);
    
    memset(adj, 0, sizeof(adj));
    memset(visited, 0, sizeof(visited));
    
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    printf("Check path from ? to ?: "); scanf("%d %d", &src, &dest);
    printf(hasPath(src, dest) ? "Path EXISTS!\n" : "NO path!\n");
    
    return 0;
}
```

---

## PROBLEM 15: Directed Graph — In-Degree and Out-Degree

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int main() {
    int adj[MAX][MAX] = {0};
    int n, edges, u, v;
    
    printf("Vertices, Edges: "); scanf("%d %d", &n, &edges);
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = 1;  // DIRECTED: u → v only
    }
    
    for (int i = 0; i < n; i++) {
        int inDeg = 0, outDeg = 0;
        
        for (int j = 0; j < n; j++) {
            // Out-degree: how many edges GO OUT from vertex i
            // Check row i — each 1 in row i means "i points to j"
            if (adj[i][j] == 1) outDeg++;
            
            // In-degree: how many edges COME IN to vertex i
            // Check column i — each 1 in column i means "j points to i"
            if (adj[j][i] == 1) inDeg++;
        }
        
        printf("Vertex %d: In-degree = %d, Out-degree = %d\n", i, inDeg, outDeg);
    }
    
    return 0;
}
```

---

> **Continued in Part B (Problems 16–30)...**
