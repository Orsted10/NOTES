# 📘 PART 4: DOUBLY LINKED LIST (DLL) — COMPLETE GUIDE

---

## 4.1 — What is a Doubly Linked List?

In a **Singly Linked List**, each node only knows about the **next** node. You can ONLY go forward.

In a **Doubly Linked List**, each node knows about both the **next** AND the **previous** node. You can go **forward AND backward**!

### Node Structure
```
        ┌──────────┬──────────┬──────────┐
        │   PREV   │   DATA   │   NEXT   │
        │(pointer) │  (value) │(pointer) │
        └──────────┴──────────┴──────────┘
```

### Code Definition
```c
#include <stdio.h>
#include <stdlib.h>

typedef struct DNode {
    int data;
    struct DNode *prev;   // Points to the PREVIOUS node
    struct DNode *next;   // Points to the NEXT node
} DNode;
```

### Visual: A DLL with 3 nodes
```
NULL <── [prev|10|next] ──⇄── [prev|20|next] ──⇄── [prev|30|next] ──> NULL
          head                                          tail
```
- First node's `prev` = NULL (nothing before it)
- Last node's `next` = NULL (nothing after it)
- Each node points both ways

### DLL vs SLL Comparison

| Feature | SLL | DLL |
|---------|-----|-----|
| Pointers per node | 1 (next) | 2 (next + prev) |
| Traversal | Forward only | Both directions |
| Delete node (given pointer to it) | O(n) — need previous | O(1) — have prev pointer! |
| Memory per node | Less | More (extra pointer) |
| Complexity | Simpler | More complex |

---

## 4.2 — Create a DLL Node

```c
DNode* createDNode(int data) {
    DNode *newNode = (DNode *)malloc(sizeof(DNode));
    if (newNode == NULL) {
        printf("Memory allocation failed!\n");
        exit(1);
    }
    newNode->data = data;
    newNode->prev = NULL;
    newNode->next = NULL;
    return newNode;
}
```

---

## 4.3 — Traversal

### Forward Traversal
```c
void printForward(DNode *head) {
    DNode *current = head;
    printf("Forward: ");
    while (current != NULL) {
        printf("%d <-> ", current->data);
        current = current->next;
    }
    printf("NULL\n");
}
```

### Backward Traversal
```c
void printBackward(DNode *head) {
    if (head == NULL) return;
    
    // Step 1: Go to the LAST node
    DNode *current = head;
    while (current->next != NULL) {
        current = current->next;
    }
    
    // Step 2: Traverse backward using prev pointers
    printf("Backward: ");
    while (current != NULL) {
        printf("%d <-> ", current->data);
        current = current->prev;
    }
    printf("NULL\n");
}
```

---

## 4.4 — Insertion

### Insert at Beginning
```c
DNode* dllInsertBeginning(DNode *head, int data) {
    DNode *newNode = createDNode(data);
    
    if (head != NULL) {
        newNode->next = head;    // New node points forward to old head
        head->prev = newNode;    // Old head points backward to new node
    }
    
    return newNode;  // New node is the new head
}
```

**Visual:**
```
BEFORE: NULL <-- [10] <-> [20] <-> [30] --> NULL
                 head

Insert 5:
Step 1: [5]->next = [10]
Step 2: [10]->prev = [5]

AFTER: NULL <-- [5] <-> [10] <-> [20] <-> [30] --> NULL
                head
```

### Insert at End
```c
DNode* dllInsertEnd(DNode *head, int data) {
    DNode *newNode = createDNode(data);
    
    if (head == NULL) return newNode;
    
    // Traverse to last node
    DNode *current = head;
    while (current->next != NULL) {
        current = current->next;
    }
    
    current->next = newNode;    // Last node points forward to new node
    newNode->prev = current;    // New node points backward to last node
    
    return head;
}
```

### Insert at Position
```c
DNode* dllInsertAtPosition(DNode *head, int data, int pos) {
    if (pos < 1) {
        printf("Invalid position!\n");
        return head;
    }
    
    if (pos == 1) return dllInsertBeginning(head, data);
    
    DNode *current = head;
    for (int i = 1; i < pos - 1 && current != NULL; i++) {
        current = current->next;
    }
    
    if (current == NULL) {
        printf("Position beyond list!\n");
        return head;
    }
    
    DNode *newNode = createDNode(data);
    
    newNode->next = current->next;    // New node points forward
    newNode->prev = current;          // New node points backward
    
    if (current->next != NULL) {
        current->next->prev = newNode;  // Next node points back to new node
    }
    current->next = newNode;            // Current node points forward to new node
    
    return head;
}
```

**Visual: Insert 25 at position 3 in [10 <-> 20 <-> 30 <-> 40]**
```
current = node(20) (position 2, the node BEFORE target position)

newNode(25)->next = current->next = node(30)
newNode(25)->prev = current = node(20)
node(30)->prev = newNode(25)
node(20)->next = newNode(25)

Result: [10] <-> [20] <-> [25] <-> [30] <-> [40]
```

---

## 4.5 — Deletion

### Delete from Beginning
```c
DNode* dllDeleteBeginning(DNode *head) {
    if (head == NULL) {
        printf("List is empty!\n");
        return NULL;
    }
    
    DNode *temp = head;
    head = head->next;
    
    if (head != NULL) {
        head->prev = NULL;   // New head has no previous node
    }
    
    printf("Deleted: %d\n", temp->data);
    free(temp);
    return head;
}
```

### Delete from End
```c
DNode* dllDeleteEnd(DNode *head) {
    if (head == NULL) {
        printf("List is empty!\n");
        return NULL;
    }
    
    // Only one node
    if (head->next == NULL) {
        printf("Deleted: %d\n", head->data);
        free(head);
        return NULL;
    }
    
    // Go to last node
    DNode *current = head;
    while (current->next != NULL) {
        current = current->next;
    }
    
    // current is the last node
    current->prev->next = NULL;  // Second-to-last node now ends the list
    printf("Deleted: %d\n", current->data);
    free(current);
    
    return head;
}
```

### Delete by Value
```c
DNode* dllDeleteByValue(DNode *head, int target) {
    DNode *current = head;
    
    // Find the node
    while (current != NULL && current->data != target) {
        current = current->next;
    }
    
    if (current == NULL) {
        printf("Value %d not found!\n", target);
        return head;
    }
    
    // If it's the head
    if (current == head) {
        head = head->next;
        if (head != NULL) head->prev = NULL;
    } else {
        // Bypass the node
        current->prev->next = current->next;
        if (current->next != NULL) {
            current->next->prev = current->prev;
        }
    }
    
    printf("Deleted: %d\n", current->data);
    free(current);
    return head;
}
```

**Visual: Delete 20 from [10 <-> 20 <-> 30]**
```
current = node(20)
current->prev = node(10), current->next = node(30)

node(10)->next = node(30)    // Skip over 20
node(30)->prev = node(10)    // Skip over 20 backward too
free(node(20))

Result: [10] <-> [30]
```

**This is the BEAUTY of DLL — to delete a node you don't need to find the previous node separately! The node already KNOWS its previous node.**

---

## 4.6 — Reverse a DLL

```c
DNode* dllReverse(DNode *head) {
    DNode *current = head;
    DNode *temp = NULL;
    
    // Swap prev and next for every node
    while (current != NULL) {
        temp = current->prev;
        current->prev = current->next;
        current->next = temp;
        
        // Move to next node (which is now in prev because we swapped!)
        current = current->prev;
    }
    
    // After the loop, temp is at the second node from end
    // The new head is temp->prev... but actually:
    if (temp != NULL) {
        head = temp->prev;
    }
    
    return head;
}
```

**Key insight:** To reverse a DLL, you just swap `prev` and `next` pointers for every node. Much simpler than SLL reversal!

---

## 4.7 — Complete DLL Driver Program

```c
int main() {
    DNode *head = NULL;
    
    // Build: 10 <-> 20 <-> 30 <-> 40
    head = dllInsertEnd(head, 10);
    head = dllInsertEnd(head, 20);
    head = dllInsertEnd(head, 30);
    head = dllInsertEnd(head, 40);
    
    printForward(head);   // Forward: 10 <-> 20 <-> 30 <-> 40 <-> NULL
    printBackward(head);  // Backward: 40 <-> 30 <-> 20 <-> 10 <-> NULL
    
    head = dllInsertBeginning(head, 5);
    printForward(head);   // Forward: 5 <-> 10 <-> 20 <-> 30 <-> 40 <-> NULL
    
    head = dllDeleteByValue(head, 20);
    printForward(head);   // Forward: 5 <-> 10 <-> 30 <-> 40 <-> NULL
    
    head = dllReverse(head);
    printForward(head);   // Forward: 40 <-> 30 <-> 10 <-> 5 <-> NULL
    
    return 0;
}
```

---

> **🎯 End of Part 4: You now know the complete Doubly Linked List — structure, create, traverse both ways, insert (3 ways), delete (3 ways), and reverse. Next: Circular Linked Lists!**
