# 📘 PART 5: CIRCULAR LINKED LISTS

---

## 5.1 — What is a Circular Linked List?

In a normal linked list, the last node points to **NULL**. In a Circular Linked List, the last node points **back to the first node**, forming a **circle/loop**.

### Circular Singly Linked List (CSLL)
```
    ┌──────────────────────────────────┐
    │                                  │
    ▼                                  │
  [10] ──→ [20] ──→ [30] ──→ [40] ────┘
  head
```
Last node (40) points to head (10) instead of NULL. There is **NO NULL** in the list!

### Circular Doubly Linked List (CDLL)
```
    ┌──────────────────────────────────────────┐
    │                                          │
    ▼                                          │
  [10] ⇄ [20] ⇄ [30] ⇄ [40] ─────────────────┘
    │                                          ▲
    └──────────────────────────────────────────┘
```
First node's prev = last node. Last node's next = first node.

### Real-World Use Cases
- **Music Playlist** — After the last song, play the first song again
- **Round-Robin Scheduling** — CPU gives each process a turn, then cycles back
- **Multiplayer Games** — Player turns cycle: Player1 → Player2 → Player3 → Player1...
- **Circular Buffer** — Fixed-size buffer that wraps around

---

## 5.2 — CIRCULAR SINGLY LINKED LIST (CSLL) OPERATIONS

### Node Structure (Same as SLL)
```c
typedef struct CNode {
    int data;
    struct CNode *next;
} CNode;
```

### Create a Node
```c
CNode* createCNode(int data) {
    CNode *newNode = (CNode *)malloc(sizeof(CNode));
    if (newNode == NULL) { printf("Malloc failed!\n"); exit(1); }
    newNode->data = data;
    newNode->next = newNode;  // Points to ITSELF (single circular node)
    return newNode;
}
```

**⚠️ Key Difference:** A single circular node's `next` points to **itself**, not NULL!

### Traversal
```c
void csllPrint(CNode *head) {
    if (head == NULL) { printf("Empty list\n"); return; }
    
    CNode *current = head;
    do {
        printf("%d -> ", current->data);
        current = current->next;
    } while (current != head);  // Stop when we come back to head
    printf("(back to %d)\n", head->data);
}
```

**⚠️ We use `do-while` instead of `while` because:**
- In a `while` loop, `current != head` is FALSE at the start (current starts at head), so the loop wouldn't execute at all!
- `do-while` executes first, then checks the condition.

### Insert at Beginning
```c
CNode* csllInsertBeginning(CNode *head, int data) {
    CNode *newNode = createCNode(data);
    
    if (head == NULL) {
        return newNode;  // First node, points to itself
    }
    
    // Find the LAST node (the one that points to head)
    CNode *last = head;
    while (last->next != head) {
        last = last->next;
    }
    
    newNode->next = head;    // New node points to old head
    last->next = newNode;    // Last node now points to new node (new head)
    
    return newNode;  // New head
}
```

### Insert at End
```c
CNode* csllInsertEnd(CNode *head, int data) {
    CNode *newNode = createCNode(data);
    
    if (head == NULL) return newNode;
    
    // Find the last node
    CNode *last = head;
    while (last->next != head) {
        last = last->next;
    }
    
    last->next = newNode;    // Old last points to new node
    newNode->next = head;    // New last points to head (circular!)
    
    return head;
}
```

### Delete from Beginning
```c
CNode* csllDeleteBeginning(CNode *head) {
    if (head == NULL) { printf("Empty!\n"); return NULL; }
    
    // Only one node
    if (head->next == head) {
        free(head);
        return NULL;
    }
    
    // Find last node
    CNode *last = head;
    while (last->next != head) {
        last = last->next;
    }
    
    CNode *temp = head;
    last->next = head->next;  // Last now points to second node
    head = head->next;        // Head moves to second node
    
    printf("Deleted: %d\n", temp->data);
    free(temp);
    return head;
}
```

### Delete from End
```c
CNode* csllDeleteEnd(CNode *head) {
    if (head == NULL) { printf("Empty!\n"); return NULL; }
    
    // Only one node
    if (head->next == head) {
        free(head);
        return NULL;
    }
    
    // Find SECOND-TO-LAST node
    CNode *current = head;
    while (current->next->next != head) {
        current = current->next;
    }
    
    printf("Deleted: %d\n", current->next->data);
    free(current->next);
    current->next = head;  // New last points to head
    
    return head;
}
```

---

## 5.3 — CIRCULAR DOUBLY LINKED LIST (CDLL) OPERATIONS

### Node Structure
```c
typedef struct CDNode {
    int data;
    struct CDNode *prev;
    struct CDNode *next;
} CDNode;
```

### Insert at End
```c
CDNode* cdllInsertEnd(CDNode *head, int data) {
    CDNode *newNode = (CDNode *)malloc(sizeof(CDNode));
    newNode->data = data;
    
    if (head == NULL) {
        newNode->next = newNode;
        newNode->prev = newNode;
        return newNode;
    }
    
    CDNode *last = head->prev;  // In CDLL, head->prev IS the last node!
    
    newNode->next = head;     // New node's next = head
    newNode->prev = last;     // New node's prev = old last
    last->next = newNode;     // Old last's next = new node
    head->prev = newNode;     // Head's prev = new node (new last)
    
    return head;
}
```

**Key insight for CDLL:** You don't need to traverse to find the last node! `head->prev` IS the last node. This makes insertions at both ends O(1)!

### Insert at Beginning
```c
CDNode* cdllInsertBeginning(CDNode *head, int data) {
    CDNode *newNode = (CDNode *)malloc(sizeof(CDNode));
    newNode->data = data;
    
    if (head == NULL) {
        newNode->next = newNode;
        newNode->prev = newNode;
        return newNode;
    }
    
    CDNode *last = head->prev;
    
    newNode->next = head;
    newNode->prev = last;
    last->next = newNode;
    head->prev = newNode;
    
    return newNode;  // newNode is the new head
}
```

### Delete from Beginning
```c
CDNode* cdllDeleteBeginning(CDNode *head) {
    if (head == NULL) return NULL;
    
    if (head->next == head) {  // Only one node
        free(head);
        return NULL;
    }
    
    CDNode *last = head->prev;
    CDNode *newHead = head->next;
    
    last->next = newHead;
    newHead->prev = last;
    
    free(head);
    return newHead;
}
```

### Traversal
```c
void cdllPrint(CDNode *head) {
    if (head == NULL) { printf("Empty\n"); return; }
    
    CDNode *current = head;
    do {
        printf("%d <-> ", current->data);
        current = current->next;
    } while (current != head);
    printf("(back to %d)\n", head->data);
}
```

---

## 5.4 — Summary of All 4 Types

```
TYPE 1: Singly Linked List (SLL)
head → [A] → [B] → [C] → NULL
✦ One direction, ends at NULL

TYPE 2: Doubly Linked List (DLL)
NULL ← [A] ⇄ [B] ⇄ [C] → NULL
✦ Two directions, ends at NULL on both sides

TYPE 3: Circular Singly Linked List (CSLL)
head → [A] → [B] → [C] → (back to A)
✦ One direction, no NULL, last→first

TYPE 4: Circular Doubly Linked List (CDLL)
[A] ⇄ [B] ⇄ [C] ⇄ (back to A)
✦ Two directions, no NULL, last⇄first
```

---

> **🎯 End of Part 5: You now know all 4 types of linked lists with their operations. Next: Time/Space complexity analysis and applications!**
