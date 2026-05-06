# 📘 BFS (Breadth-First Search) — Complete Deep Dive

---

## 3.1 — What is BFS?

### 🧠 Analogy: Ripple in Water
Drop a stone in a pond. The ripple spreads in **circles** — first the closest water, then the next ring, then the next. BFS explores a graph the SAME way:
- Visit the **starting vertex** first
- Then visit ALL vertices that are **1 edge away**
- Then ALL vertices that are **2 edges away**
- Then **3 edges away**... and so on

### 📖 Formal Definition
> **BFS is a graph traversal algorithm that explores vertices LEVEL BY LEVEL. Starting from a source vertex, it visits all neighbors first (level 1), then all neighbors of neighbors (level 2), and so on. It uses a QUEUE data structure.**

### Key Properties
- Uses a **QUEUE** (First-In-First-Out)
- Explores **level by level** (breadth-wise, hence "breadth-first")
- Finds the **shortest path** in unweighted graphs
- Time Complexity: **O(V + E)** using adjacency list
- Space Complexity: **O(V)** for the visited array + queue

---

## 3.2 — How BFS Works (Step-by-Step with Tracing)

### The Algorithm (English)
```
1. Create a queue and a visited[] array (all set to false/0)
2. Mark the starting vertex as visited and add it to the queue
3. WHILE the queue is NOT empty:
   a. Remove (dequeue) a vertex from the front of the queue
   b. Process it (print it)
   c. For each NEIGHBOR of this vertex:
      - If the neighbor is NOT visited:
        - Mark it as visited
        - Add it to the queue
4. Done! All reachable vertices have been visited.
```

### Detailed Tracing on an Example Graph

```
Graph:
    0 ─── 1 ─── 4
    │     │
    2 ─── 3

Starting vertex: 0

Adjacency List:
0: [1, 2]
1: [0, 3, 4]
2: [0, 3]
3: [1, 2]
4: [1]
```

```
visited[] = [0, 0, 0, 0, 0]  (all unvisited)
Queue: []

STEP 1: Start at vertex 0
  Mark visited[0] = 1
  Enqueue 0
  visited[] = [1, 0, 0, 0, 0]
  Queue: [0]

STEP 2: Dequeue 0 → Process (print "0")
  Neighbors of 0: {1, 2}
  - 1 not visited → mark visited, enqueue
  - 2 not visited → mark visited, enqueue
  visited[] = [1, 1, 1, 0, 0]
  Queue: [1, 2]
  Output so far: 0

STEP 3: Dequeue 1 → Process (print "1")
  Neighbors of 1: {0, 3, 4}
  - 0 already visited → SKIP
  - 3 not visited → mark visited, enqueue
  - 4 not visited → mark visited, enqueue
  visited[] = [1, 1, 1, 1, 1]
  Queue: [2, 3, 4]
  Output so far: 0 1

STEP 4: Dequeue 2 → Process (print "2")
  Neighbors of 2: {0, 3}
  - 0 already visited → SKIP
  - 3 already visited → SKIP
  Queue: [3, 4]
  Output so far: 0 1 2

STEP 5: Dequeue 3 → Process (print "3")
  Neighbors of 3: {1, 2}
  - 1 already visited → SKIP
  - 2 already visited → SKIP
  Queue: [4]
  Output so far: 0 1 2 3

STEP 6: Dequeue 4 → Process (print "4")
  Neighbors of 4: {1}
  - 1 already visited → SKIP
  Queue: []  (EMPTY → DONE!)
  Output so far: 0 1 2 3 4

FINAL BFS ORDER: 0 → 1 → 2 → 3 → 4
```

---

## 3.3 — BFS Code Using Adjacency Matrix (Every Line Explained)

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX 100

// ============================================
// We implement a simple queue using an array.
// front = index of the front element
// rear  = index of the rear element
// When front > rear, queue is empty
// ============================================
int queue[MAX];    // Array to hold queue elements
int front = -1;    // Index of front element (-1 means empty)
int rear = -1;     // Index of rear element (-1 means empty)

// ============================================
// FUNCTION: enqueue
// PURPOSE:  Add a vertex to the BACK of the queue
// WHY:      BFS uses FIFO order — new vertices go to the end
// ============================================
void enqueue(int vertex) {
    // If queue is empty, set front to 0
    if (front == -1) front = 0;
    
    // Move rear forward and place the vertex
    rear++;
    queue[rear] = vertex;
}

// ============================================
// FUNCTION: dequeue
// PURPOSE:  Remove and return the vertex at the FRONT of the queue
// WHY:      BFS processes vertices in the order they were added (FIFO)
// ============================================
int dequeue() {
    int vertex = queue[front];  // Get the front element
    front++;                     // Move front forward
    
    // If front passed rear, queue is empty — reset
    if (front > rear) {
        front = -1;
        rear = -1;
    }
    
    return vertex;
}

// ============================================
// FUNCTION: isQueueEmpty
// PURPOSE:  Check if the queue has no elements
// ============================================
int isQueueEmpty() {
    return front == -1;
}

// ============================================
// FUNCTION: bfs
// PURPOSE:  Perform BFS traversal starting from 'start' vertex
// PARAMS:   adjMatrix = the adjacency matrix
//           vertices  = total number of vertices in the graph
//           start     = the vertex to begin BFS from
//
// HOW IT WORKS:
//   1. Create a visited[] array — tracks which vertices we've already seen
//   2. Mark start as visited and enqueue it
//   3. Loop: dequeue a vertex, print it, then enqueue all its unvisited neighbors
//   4. Repeat until queue is empty
// ============================================
void bfs(int adjMatrix[MAX][MAX], int vertices, int start) {
    // STEP 1: Create visited array — all initialized to 0 (false = not visited)
    int visited[MAX] = {0};
    // visited[i] = 0 means vertex i has NOT been visited
    // visited[i] = 1 means vertex i HAS been visited
    
    // STEP 2: Mark the starting vertex as visited
    visited[start] = 1;
    // WHY? If we don't mark it, we might add it to the queue again later
    // when another vertex has an edge back to it
    
    // STEP 3: Add starting vertex to the queue
    enqueue(start);
    // The queue now has our starting point — the BFS begins from here
    
    printf("BFS Traversal: ");
    
    // STEP 4: Process vertices until queue is empty
    while (!isQueueEmpty()) {
        // STEP 4a: Remove the front vertex from the queue
        int current = dequeue();
        // This is the vertex we're "visiting" right now
        
        // STEP 4b: Process it (here, we print it)
        printf("%d ", current);
        
        // STEP 4c: Look at ALL possible neighbors of current vertex
        // In adjacency matrix, we check EVERY column in current's row
        for (int i = 0; i < vertices; i++) {
            // Check: is there an edge from 'current' to 'i'?
            // AND has 'i' NOT been visited yet?
            if (adjMatrix[current][i] == 1 && visited[i] == 0) {
                // YES — this is an unvisited neighbor!
                
                // Mark it as visited NOW (before enqueuing)
                // WHY NOW? To prevent adding it to the queue MULTIPLE times
                // If two vertices both connect to vertex i, without marking
                // it visited here, both would enqueue it → duplicate processing!
                visited[i] = 1;
                
                // Add it to the queue — it will be processed later
                enqueue(i);
            }
        }
    }
    printf("\n");
}

int main() {
    int adjMatrix[MAX][MAX] = {0};  // All zeros — no edges
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
    
    bfs(adjMatrix, vertices, 0);
    // Output: BFS Traversal: 0 1 2 3 4
    
    return 0;
}
```

---

## 3.4 — BFS Code Using Adjacency List

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX 100

// --- Node for adjacency list ---
typedef struct Node {
    int dest;
    struct Node *next;
} Node;

// --- Graph structure ---
typedef struct Graph {
    int numVertices;
    Node **adjLists;   // Array of linked list heads
} Graph;

Node* createNode(int dest) {
    Node *n = (Node *)malloc(sizeof(Node));
    n->dest = dest;
    n->next = NULL;
    return n;
}

Graph* createGraph(int v) {
    Graph *g = (Graph *)malloc(sizeof(Graph));
    g->numVertices = v;
    g->adjLists = (Node **)malloc(v * sizeof(Node *));
    for (int i = 0; i < v; i++) g->adjLists[i] = NULL;
    return g;
}

void addEdge(Graph *g, int src, int dest) {
    Node *n = createNode(dest);
    n->next = g->adjLists[src];
    g->adjLists[src] = n;
    
    n = createNode(src);
    n->next = g->adjLists[dest];
    g->adjLists[dest] = n;
}

// --- Queue ---
int queue[MAX], front = -1, rear = -1;
void enqueue(int v) { if(front==-1) front=0; queue[++rear]=v; }
int dequeue() { int v=queue[front++]; if(front>rear){front=-1;rear=-1;} return v; }
int isEmpty() { return front==-1; }

// ============================================
// BFS using Adjacency List
// KEY DIFFERENCE from matrix version:
//   Instead of checking ALL V columns (matrix row),
//   we only traverse the linked list of actual neighbors.
//   This is O(degree) instead of O(V) per vertex.
//   Total: O(V + E) instead of O(V²)
// ============================================
void bfs(Graph *g, int start) {
    int visited[MAX] = {0};
    
    visited[start] = 1;
    enqueue(start);
    
    printf("BFS: ");
    while (!isEmpty()) {
        int current = dequeue();
        printf("%d ", current);
        
        // Traverse the linked list of neighbors
        // Only visits ACTUAL neighbors — much faster for sparse graphs!
        Node *temp = g->adjLists[current];
        while (temp != NULL) {
            int neighbor = temp->dest;
            if (!visited[neighbor]) {
                visited[neighbor] = 1;
                enqueue(neighbor);
            }
            temp = temp->next;
        }
    }
    printf("\n");
}

int main() {
    Graph *g = createGraph(6);
    addEdge(g, 0, 1); addEdge(g, 0, 2);
    addEdge(g, 1, 3); addEdge(g, 2, 4);
    addEdge(g, 3, 5); addEdge(g, 4, 5);
    
    bfs(g, 0);  // Output: BFS: 0 2 1 4 3 5  (order depends on adjacency list order)
    return 0;
}
```

---

## 3.5 — BFS Applications

| Application | How BFS Helps |
|------------|---------------|
| **Shortest path (unweighted)** | BFS guarantees shortest path in # of edges |
| **Level-order traversal of tree** | BFS naturally visits level by level |
| **Connected components** | Run BFS from each unvisited vertex |
| **Cycle detection (undirected)** | If we visit an already-visited neighbor (not parent), cycle exists |
| **Bipartite graph check** | Try to 2-color the graph using BFS levels |
| **Web crawler** | Visit pages breadth-first from a starting URL |
| **Social network "People you may know"** | Friends-of-friends = BFS level 2 |
| **GPS navigation** | Find shortest route |

---

> **🎯 End of BFS. You now deeply understand the concept, algorithm, tracing, both matrix and list implementations with every line explained. NEXT → DFS!**
