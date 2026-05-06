# 📘 GRAPH CODING QUESTIONS — Part B (Problems 16–30)

---

## PROBLEM 16: BFS Using Adjacency List (Full Implementation)

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define MAX 100

typedef struct Node { int dest; struct Node *next; } Node;
Node *adjList[MAX];
int visited[MAX];
int queue[MAX], front=-1, rear=-1;

void enq(int v){if(front==-1)front=0;queue[++rear]=v;}
int deq(){int v=queue[front++];if(front>rear){front=-1;rear=-1;}return v;}
int empty(){return front==-1;}

// Add undirected edge using linked list insertion at head
void addEdge(int u, int v) {
    Node *n = (Node*)malloc(sizeof(Node));
    n->dest = v; n->next = adjList[u]; adjList[u] = n;
    n = (Node*)malloc(sizeof(Node));
    n->dest = u; n->next = adjList[v]; adjList[v] = n;
}

// BFS with adjacency list — only visits ACTUAL neighbors, not all V
void bfs(int start) {
    memset(visited, 0, sizeof(visited));
    front = rear = -1;
    
    visited[start] = 1;
    enq(start);
    
    printf("BFS: ");
    while (!empty()) {
        int curr = deq();
        printf("%d ", curr);
        
        // Traverse linked list of neighbors — O(degree), not O(V)!
        Node *temp = adjList[curr];
        while (temp) {
            if (!visited[temp->dest]) {
                visited[temp->dest] = 1;
                enq(temp->dest);
            }
            temp = temp->next;
        }
    }
    printf("\n");
}

int main() {
    int n, edges, u, v;
    scanf("%d %d", &n, &edges);
    for (int i = 0; i < n; i++) adjList[i] = NULL;
    for (int i = 0; i < edges; i++) { scanf("%d %d", &u, &v); addEdge(u,v); }
    bfs(0);
    return 0;
}
```

---

## PROBLEM 17: DFS Using Adjacency List (Recursive)

```c
#include <stdio.h>
#include <stdlib.h>
#define MAX 100

typedef struct Node { int dest; struct Node *next; } Node;
Node *adjList[MAX];
int visited[MAX] = {0};

void addEdge(int u, int v) {
    Node *n = (Node*)malloc(sizeof(Node));
    n->dest = v; n->next = adjList[u]; adjList[u] = n;
    n = (Node*)malloc(sizeof(Node));
    n->dest = u; n->next = adjList[v]; adjList[v] = n;
}

void dfs(int v) {
    visited[v] = 1;
    printf("%d ", v);
    
    Node *temp = adjList[v];
    while (temp) {
        if (!visited[temp->dest])
            dfs(temp->dest);
        temp = temp->next;
    }
}

int main() {
    int n, edges, u, v;
    scanf("%d %d", &n, &edges);
    for (int i = 0; i < n; i++) adjList[i] = NULL;
    for (int i = 0; i < edges; i++) { scanf("%d %d", &u, &v); addEdge(u,v); }
    printf("DFS: "); dfs(0); printf("\n");
    return 0;
}
```

---

## PROBLEM 18: Count Number of Edges

```c
#include <stdio.h>
#define MAX 100

int main() {
    int adj[MAX][MAX] = {0};
    int n, edges, u, v;
    scanf("%d %d", &n, &edges);
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    // Count all 1s in the matrix and divide by 2
    // WHY divide? Each undirected edge creates TWO 1s (matrix[i][j] and matrix[j][i])
    int count = 0;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            if (adj[i][j]) count++;
    
    printf("Total edges: %d\n", count / 2);
    // For directed graphs, DON'T divide by 2
    return 0;
}
```

---

## PROBLEM 19: BFS Level-Order (Print Level of Each Vertex)

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], level[MAX], visited[MAX];
int queue[MAX], front=-1, rear=-1;

void enq(int v){if(front==-1)front=0;queue[++rear]=v;}
int deq(){int v=queue[front++];if(front>rear){front=-1;rear=-1;}return v;}
int empty(){return front==-1;}

// BFS that tracks the LEVEL (distance from source) of each vertex
void bfsLevel(int n, int start) {
    memset(visited, 0, sizeof(visited));
    memset(level, -1, sizeof(level));
    
    visited[start] = 1;
    level[start] = 0;  // Source is at level 0
    enq(start);
    
    while (!empty()) {
        int curr = deq();
        for (int i = 0; i < n; i++) {
            if (adj[curr][i] && !visited[i]) {
                visited[i] = 1;
                level[i] = level[curr] + 1;  // One level deeper than parent
                enq(i);
            }
        }
    }
    
    printf("Vertex : Level\n");
    for (int i = 0; i < n; i++)
        printf("  %d    :  %d\n", i, level[i]);
}

int main() {
    int n=6;
    memset(adj, 0, sizeof(adj));
    // Graph: 0-1, 0-2, 1-3, 2-4, 3-5, 4-5
    adj[0][1]=adj[1][0]=1; adj[0][2]=adj[2][0]=1;
    adj[1][3]=adj[3][1]=1; adj[2][4]=adj[4][2]=1;
    adj[3][5]=adj[5][3]=1; adj[4][5]=adj[5][4]=1;
    
    bfsLevel(n, 0);
    // Vertex 0: level 0, Vertex 1: level 1, Vertex 2: level 1,
    // Vertex 3: level 2, Vertex 4: level 2, Vertex 5: level 3
    return 0;
}
```

---

## PROBLEM 20: Check if Graph is Connected

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX];
int n;

void dfs(int v) {
    visited[v] = 1;
    for (int i = 0; i < n; i++)
        if (adj[v][i] && !visited[i]) dfs(i);
}

int main() {
    int edges, u, v;
    scanf("%d %d", &n, &edges);
    memset(adj, 0, sizeof(adj));
    memset(visited, 0, sizeof(visited));
    
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    // Run DFS from vertex 0
    dfs(0);
    
    // Check if ALL vertices were visited
    // If yes → connected. If any unvisited → disconnected.
    int connected = 1;
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            connected = 0;
            break;
        }
    }
    
    printf(connected ? "Graph is CONNECTED\n" : "Graph is DISCONNECTED\n");
    return 0;
}
```

---

## PROBLEM 21: Transpose of a Directed Graph

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

// Transpose = reverse all edge directions
// If original has A→B, transpose has B→A
int main() {
    int adj[MAX][MAX] = {0}, trans[MAX][MAX] = {0};
    int n, edges, u, v;
    
    scanf("%d %d", &n, &edges);
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = 1;
    }
    
    // Create transpose: swap rows and columns
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            trans[j][i] = adj[i][j];  // reverse the direction
    
    printf("Original:\n");
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) printf("%d ", adj[i][j]);
        printf("\n");
    }
    
    printf("Transpose:\n");
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) printf("%d ", trans[i][j]);
        printf("\n");
    }
    
    return 0;
}
```

---

## PROBLEM 22: Weighted Graph — Adjacency Matrix

```c
#include <stdio.h>
#include <string.h>
#define MAX 100
#define INF 99999  // Represent "no edge" with a large number

int main() {
    int adj[MAX][MAX];
    int n, edges, u, v, w;
    
    scanf("%d %d", &n, &edges);
    
    // Initialize all to INF (no edges)
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            adj[i][j] = (i == j) ? 0 : INF;
    // Distance from vertex to itself = 0
    // Distance to others = infinity (no direct edge yet)
    
    for (int i = 0; i < edges; i++) {
        scanf("%d %d %d", &u, &v, &w);  // u, v = vertices, w = weight
        adj[u][v] = w;
        adj[v][u] = w;  // undirected
    }
    
    printf("Weighted Adjacency Matrix:\n");
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (adj[i][j] == INF) printf("INF ");
            else printf("%3d ", adj[i][j]);
        }
        printf("\n");
    }
    return 0;
}
```

---

## PROBLEM 23: DFS Iterative Using Stack

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX];
int stack[MAX], top = -1;

void push(int v) { stack[++top] = v; }
int pop() { return stack[top--]; }
int isEmpty() { return top == -1; }

// Iterative DFS — uses explicit stack instead of recursion
// Useful when graph is very deep (avoids stack overflow from recursion)
void dfsIterative(int n, int start) {
    memset(visited, 0, sizeof(visited));
    push(start);
    
    printf("DFS (iterative): ");
    while (!isEmpty()) {
        int curr = pop();
        
        if (visited[curr]) continue;  // Skip if already visited
        
        visited[curr] = 1;
        printf("%d ", curr);
        
        // Push neighbors in REVERSE order so smallest is processed first
        for (int i = n - 1; i >= 0; i--) {
            if (adj[curr][i] && !visited[i])
                push(i);
        }
    }
    printf("\n");
}

int main() {
    int n=5;
    memset(adj, 0, sizeof(adj));
    adj[0][1]=adj[1][0]=1; adj[0][2]=adj[2][0]=1;
    adj[1][3]=adj[3][1]=1; adj[1][4]=adj[4][1]=1;
    adj[2][3]=adj[3][2]=1;
    
    dfsIterative(n, 0);  // Output: 0 1 3 2 4
    return 0;
}
```

---

## PROBLEM 24: Check if Graph is a Tree

A graph is a tree if: (1) It's connected, AND (2) It has no cycles (equivalently, has exactly V-1 edges).

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX];
int n;

int dfsCycle(int v, int parent) {
    visited[v] = 1;
    for (int i = 0; i < n; i++) {
        if (adj[v][i]) {
            if (!visited[i]) {
                if (dfsCycle(i, v)) return 1;
            } else if (i != parent) {
                return 1;  // Cycle detected
            }
        }
    }
    return 0;
}

int main() {
    int edges, u, v;
    scanf("%d %d", &n, &edges);
    memset(adj, 0, sizeof(adj));
    memset(visited, 0, sizeof(visited));
    
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    // Check 1: No cycles
    int hasCycle = dfsCycle(0, -1);
    
    // Check 2: All vertices visited (connected)
    int connected = 1;
    for (int i = 0; i < n; i++)
        if (!visited[i]) { connected = 0; break; }
    
    if (!hasCycle && connected)
        printf("Graph IS a tree!\n");
    else
        printf("Graph is NOT a tree. %s%s\n",
            hasCycle ? "Has cycle. " : "",
            !connected ? "Not connected." : "");
    
    return 0;
}
```

---

## PROBLEM 25: Count Vertices at Given Level (BFS)

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], level[MAX], visited[MAX];
int queue[MAX], front=-1, rear=-1;
void enq(int v){if(front==-1)front=0;queue[++rear]=v;}
int deq(){int v=queue[front++];if(front>rear){front=-1;rear=-1;}return v;}
int empty(){return front==-1;}

int countAtLevel(int n, int start, int targetLevel) {
    memset(visited, 0, sizeof(visited));
    memset(level, -1, sizeof(level));
    front = rear = -1;
    
    visited[start] = 1; level[start] = 0; enq(start);
    
    while (!empty()) {
        int curr = deq();
        for (int i = 0; i < n; i++) {
            if (adj[curr][i] && !visited[i]) {
                visited[i] = 1;
                level[i] = level[curr] + 1;
                enq(i);
            }
        }
    }
    
    int count = 0;
    for (int i = 0; i < n; i++)
        if (level[i] == targetLevel) count++;
    return count;
}

int main() {
    int n=6;
    memset(adj, 0, sizeof(adj));
    adj[0][1]=adj[1][0]=1; adj[0][2]=adj[2][0]=1;
    adj[1][3]=adj[3][1]=1; adj[1][4]=adj[4][1]=1;
    adj[2][5]=adj[5][2]=1;
    
    printf("Vertices at level 1: %d\n", countAtLevel(n, 0, 1));  // 2 (vertices 1,2)
    printf("Vertices at level 2: %d\n", countAtLevel(n, 0, 2));  // 3 (vertices 3,4,5)
    return 0;
}
```

---

## PROBLEM 26: Print Adjacency List from Adjacency Matrix

```c
#include <stdio.h>
#define MAX 100

int main() {
    int adj[MAX][MAX] = {0};
    int n=5;
    
    adj[0][1]=adj[1][0]=1; adj[0][2]=adj[2][0]=1;
    adj[1][3]=adj[3][1]=1; adj[2][3]=adj[3][2]=1;
    adj[3][4]=adj[4][3]=1;
    
    // Convert matrix to list representation (print format)
    printf("Adjacency List from Matrix:\n");
    for (int i = 0; i < n; i++) {
        printf("Vertex %d:", i);
        for (int j = 0; j < n; j++) {
            if (adj[i][j] == 1)
                printf(" -> %d", j);
        }
        printf(" -> NULL\n");
    }
    return 0;
}
```

---

## PROBLEM 27: BFS for Disconnected Graph

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX];
int queue[MAX], front=-1, rear=-1;
void enq(int v){if(front==-1)front=0;queue[++rear]=v;}
int deq(){int v=queue[front++];if(front>rear){front=-1;rear=-1;}return v;}
int empty(){return front==-1;}

void bfs(int start, int n) {
    visited[start] = 1;
    enq(start);
    while (!empty()) {
        int c = deq();
        printf("%d ", c);
        for (int i = 0; i < n; i++)
            if (adj[c][i] && !visited[i]) { visited[i]=1; enq(i); }
    }
}

// Run BFS for ALL components — handles disconnected graphs
void bfsComplete(int n) {
    memset(visited, 0, sizeof(visited));
    printf("Complete BFS (all components): ");
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            // Start a new BFS from this unvisited vertex
            front = rear = -1;  // Reset queue for new component
            bfs(i, n);
        }
    }
    printf("\n");
}

int main() {
    int n=6;
    memset(adj, 0, sizeof(adj));
    // Component 1: 0-1-2
    adj[0][1]=adj[1][0]=1; adj[1][2]=adj[2][1]=1;
    // Component 2: 3-4 (disconnected from component 1)
    adj[3][4]=adj[4][3]=1;
    // Vertex 5: isolated
    
    bfsComplete(n);  // Output: 0 1 2 3 4 5
    return 0;
}
```

---

## PROBLEM 28: Find if Undirected Graph has Euler Path/Circuit

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX];
int n;

void dfs(int v) { visited[v]=1; for(int i=0;i<n;i++) if(adj[v][i]&&!visited[i]) dfs(i); }

int main() {
    int edges, u, v;
    scanf("%d %d", &n, &edges);
    memset(adj, 0, sizeof(adj));
    
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = adj[v][u] = 1;
    }
    
    // Count vertices with ODD degree
    int oddCount = 0;
    for (int i = 0; i < n; i++) {
        int deg = 0;
        for (int j = 0; j < n; j++) if (adj[i][j]) deg++;
        if (deg % 2 != 0) oddCount++;
    }
    
    // Check connectivity
    memset(visited, 0, sizeof(visited));
    // Find first vertex with non-zero degree
    int start = -1;
    for (int i = 0; i < n; i++) {
        int deg = 0;
        for (int j = 0; j < n; j++) if(adj[i][j]) deg++;
        if (deg > 0) { start = i; break; }
    }
    
    if (start == -1) { printf("No edges — trivially Eulerian.\n"); return 0; }
    
    dfs(start);
    int connected = 1;
    for (int i = 0; i < n; i++) {
        int deg = 0;
        for (int j = 0; j < n; j++) if(adj[i][j]) deg++;
        if (deg > 0 && !visited[i]) { connected = 0; break; }
    }
    
    // Euler's theorem:
    // 0 odd-degree vertices → Euler CIRCUIT (start = end)
    // 2 odd-degree vertices → Euler PATH (different start and end)
    // Otherwise → neither
    if (!connected)
        printf("Not connected among edge-bearing vertices — no Euler path/circuit.\n");
    else if (oddCount == 0)
        printf("Graph has an EULER CIRCUIT!\n");
    else if (oddCount == 2)
        printf("Graph has an EULER PATH!\n");
    else
        printf("Graph has NEITHER Euler path nor circuit. (%d odd-degree vertices)\n", oddCount);
    
    return 0;
}
```

---

## PROBLEM 29: Menu-Driven Graph Program

```c
#include <stdio.h>
#include <string.h>
#define MAX 100

int adj[MAX][MAX], visited[MAX], n;
int queue[MAX], front=-1, rear=-1;
void enq(int v){if(front==-1)front=0;queue[++rear]=v;}
int deq(){int v=queue[front++];if(front>rear){front=-1;rear=-1;}return v;}
int empty(){return front==-1;}

void bfs(int s) {
    memset(visited,0,sizeof(visited));
    front=rear=-1; visited[s]=1; enq(s);
    printf("BFS: ");
    while(!empty()){int c=deq();printf("%d ",c);
        for(int i=0;i<n;i++) if(adj[c][i]&&!visited[i]){visited[i]=1;enq(i);}}
    printf("\n");
}
void dfs(int v){visited[v]=1;printf("%d ",v);
    for(int i=0;i<n;i++) if(adj[v][i]&&!visited[i]) dfs(i);}

int main() {
    int ch, u, v;
    printf("Enter vertices: "); scanf("%d", &n);
    memset(adj, 0, sizeof(adj));
    
    while (1) {
        printf("\n1.Add Edge 2.Display Matrix 3.BFS 4.DFS 5.Degree 6.Exit\nChoice: ");
        scanf("%d", &ch);
        switch(ch) {
            case 1: printf("Edge (u v): "); scanf("%d %d",&u,&v);
                    adj[u][v]=adj[v][u]=1; break;
            case 2: for(int i=0;i<n;i++){for(int j=0;j<n;j++)printf("%d ",adj[i][j]);printf("\n");} break;
            case 3: printf("Start: "); scanf("%d",&u); bfs(u); break;
            case 4: printf("Start: "); scanf("%d",&u); memset(visited,0,sizeof(visited));
                    printf("DFS: "); dfs(u); printf("\n"); break;
            case 5: for(int i=0;i<n;i++){int d=0;for(int j=0;j<n;j++)if(adj[i][j])d++;
                    printf("Vertex %d: degree %d\n",i,d);} break;
            case 6: return 0;
        }
    }
}
```

---

## PROBLEM 30: BFS and DFS with Adjacency List — Complete Program

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define MAX 100

typedef struct Node { int dest; struct Node *next; } Node;

typedef struct Graph {
    int V;
    Node *adj[MAX];
} Graph;

void initGraph(Graph *g, int v) {
    g->V = v;
    for (int i = 0; i < v; i++) g->adj[i] = NULL;
}

void addEdge(Graph *g, int u, int v) {
    Node *n=(Node*)malloc(sizeof(Node)); n->dest=v; n->next=g->adj[u]; g->adj[u]=n;
    n=(Node*)malloc(sizeof(Node)); n->dest=u; n->next=g->adj[v]; g->adj[v]=n;
}

// --- BFS ---
int queue[MAX], front, rear;
void enq(int v){if(front==-1)front=0;queue[++rear]=v;}
int deq(){int v=queue[front++];if(front>rear){front=-1;rear=-1;}return v;}
int emp(){return front==-1;}

void bfs(Graph *g, int start) {
    int vis[MAX]={0}; front=rear=-1;
    vis[start]=1; enq(start);
    printf("BFS: ");
    while(!emp()){
        int c=deq(); printf("%d ",c);
        for(Node *t=g->adj[c]; t; t=t->next)
            if(!vis[t->dest]){vis[t->dest]=1; enq(t->dest);}
    }
    printf("\n");
}

// --- DFS ---
int vis2[MAX];
void dfs(Graph *g, int v) {
    vis2[v]=1; printf("%d ",v);
    for(Node *t=g->adj[v]; t; t=t->next)
        if(!vis2[t->dest]) dfs(g, t->dest);
}

int main() {
    Graph g;
    initGraph(&g, 7);
    
    addEdge(&g,0,1); addEdge(&g,0,2); addEdge(&g,1,3);
    addEdge(&g,1,4); addEdge(&g,2,5); addEdge(&g,2,6);
    
    printf("Graph (Adjacency List):\n");
    for(int i=0;i<g.V;i++){
        printf("%d:",i);
        for(Node *t=g.adj[i];t;t=t->next) printf(" -> %d",t->dest);
        printf(" -> NULL\n");
    }
    printf("\n");
    
    bfs(&g, 0);
    
    memset(vis2, 0, sizeof(vis2));
    printf("DFS: "); dfs(&g, 0); printf("\n");
    
    return 0;
}
```

---

> **🎯 END OF ALL GRAPH NOTES!**
> 
> **Complete Coverage:**
> - ✅ Graph Foundations & Terminology (File 10)
> - ✅ Adjacency Matrix & List representations with full code (File 11)
> - ✅ BFS — concept, tracing, matrix & list code (File 12)
> - ✅ DFS — concept, tracing, recursive & iterative code (File 13)
> - ✅ 65 Theory/VIVA Questions (File 14)
> - ✅ 30 Coding Problems with every line explained (Files 15A + 15B)
