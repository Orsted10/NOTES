# 📘 PART 2: SINGLY LINKED LIST — ALL OPERATIONS

---

## Common Setup (Used in ALL Code Below)

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node *next;
} Node;
```

---

## 2.1 — CREATE a New Node (Helper Function)

This function is used by almost every other operation.

```c
// Creates a new node with given data and returns pointer to it
Node* createNode(int data) {
    // Step 1: Ask OS for memory to hold one node
    Node *newNode = (Node *)malloc(sizeof(Node));
    
    // Step 2: Check if memory was given (malloc returns NULL if it fails)
    if (newNode == NULL) {
        printf("Error: Memory allocation failed!\n");
        exit(1);  // Terminate the program
    }
    
    // Step 3: Fill in the data
    newNode->data = data;
    
    // Step 4: New node doesn't point anywhere yet
    newNode->next = NULL;
    
    // Step 5: Return the address of this new node
    return newNode;
}
```

**Line-by-line:**
- `malloc(sizeof(Node))` — Allocates exactly enough bytes for one Node struct (data + pointer)
- `(Node *)` — malloc returns `void *`, we cast it to `Node *`
- `exit(1)` — If malloc fails (out of memory), we stop the program
- `newNode->next = NULL` — Safety: don't leave pointers uninitialized (dangling pointers are dangerous!)

---

## 2.2 — TRAVERSAL (Printing the List)

**What:** Visit every node from head to end and print its data.
**Time Complexity:** O(n) — must visit all n nodes.

```c
void printList(Node *head) {
    // Step 1: Start from the head
    Node *current = head;
    
    // Step 2: Keep going until we reach NULL (end of list)
    while (current != NULL) {
        printf("%d -> ", current->data);
        
        // Step 3: Move to the next node
        current = current->next;
    }
    printf("NULL\n");
}
```

**How it works visually:**
```
List: 10 -> 20 -> 30 -> NULL

Iteration 1: current = Node(10), print 10, move to Node(20)
Iteration 2: current = Node(20), print 20, move to Node(30)
Iteration 3: current = Node(30), print 30, move to NULL
Loop ends:   current = NULL, stop.

Output: 10 -> 20 -> 30 -> NULL
```

---

## 2.3 — INSERTION (4 Variations)

### 2.3.1 — Insert at the BEGINNING

**Why it's special:** We must update the `head` pointer!

```c
// We pass Node **head (pointer to pointer) because we need to MODIFY head
Node* insertAtBeginning(Node *head, int data) {
    // Step 1: Create the new node
    Node *newNode = createNode(data);
    
    // Step 2: New node's next should point to current head
    //         (the new node is going BEFORE the current first node)
    newNode->next = head;
    
    // Step 3: Update head to point to new node
    //         (new node IS the new first node now)
    head = newNode;
    
    // Step 4: Return the updated head
    return head;
}

// Usage:
// head = insertAtBeginning(head, 5);
```

**Visual:**
```
BEFORE: head -> [10] -> [20] -> [30] -> NULL
Insert 5 at beginning:

Step 1: Create [5|NULL]
Step 2: [5] -> [10] -> [20] -> [30] -> NULL
Step 3: head -> [5] -> [10] -> [20] -> [30] -> NULL

AFTER: head -> [5] -> [10] -> [20] -> [30] -> NULL
```

**Why pointer-to-pointer OR return head?**
If we just pass `Node *head`, changes to `head` inside the function are LOCAL (C passes by value). Two solutions:
1. **Return the new head** (we use this approach — simpler)
2. **Pass `Node **head`** (pointer to pointer — modifies original directly)

```c
// Alternative: Using pointer to pointer
void insertAtBeginningV2(Node **head, int data) {
    Node *newNode = createNode(data);
    newNode->next = *head;  // *head = current head pointer
    *head = newNode;        // Update the original head
}
// Usage: insertAtBeginningV2(&head, 5);
```

---

### 2.3.2 — Insert at the END

```c
Node* insertAtEnd(Node *head, int data) {
    Node *newNode = createNode(data);
    
    // Special case: If list is empty, new node IS the head
    if (head == NULL) {
        return newNode;
    }
    
    // Step 1: Find the last node (the one whose next is NULL)
    Node *current = head;
    while (current->next != NULL) {  // Stop AT the last node, not past it
        current = current->next;
    }
    
    // Step 2: Make the last node point to our new node
    current->next = newNode;
    
    return head;  // Head hasn't changed
}
```

**Visual:**
```
BEFORE: head -> [10] -> [20] -> [30] -> NULL
Insert 40 at end:

Step 1: Traverse to find last node (30)
         current starts at 10 -> moves to 20 -> moves to 30 (30->next is NULL, stop!)
Step 2: [30]->next = newNode(40)

AFTER: head -> [10] -> [20] -> [30] -> [40] -> NULL
```

**Why `current->next != NULL` and NOT `current != NULL`?**
- If we use `current != NULL`, when the loop ends, `current` is NULL — we've gone PAST the last node!
- We need to STOP **at** the last node so we can modify its `next` pointer.

---

### 2.3.3 — Insert at a SPECIFIC POSITION

Position is 1-indexed (position 1 = first element).

```c
Node* insertAtPosition(Node *head, int data, int position) {
    // Validate position
    if (position < 1) {
        printf("Invalid position!\n");
        return head;
    }
    
    // Special case: Insert at position 1 = insert at beginning
    if (position == 1) {
        return insertAtBeginning(head, data);
    }
    
    Node *newNode = createNode(data);
    
    // Step 1: Traverse to the node just BEFORE the target position
    Node *current = head;
    for (int i = 1; i < position - 1 && current != NULL; i++) {
        current = current->next;
    }
    
    // Step 2: Check if position is valid (not beyond the list)
    if (current == NULL) {
        printf("Position %d is beyond the list length!\n", position);
        free(newNode);
        return head;
    }
    
    // Step 3: Insert the new node
    //   newNode's next = what current was pointing to
    //   current's next = newNode
    newNode->next = current->next;
    current->next = newNode;
    
    return head;
}
```

**Visual: Insert 25 at position 3**
```
BEFORE: head -> [10] -> [20] -> [30] -> [40] -> NULL
                 pos1    pos2    pos3    pos4

We want 25 at position 3, so we need to reach node at position 2 (which is 20).

Step 1: current starts at head (pos 1)
        i=1: i < 3-1=2? YES → current = current->next (now at 20, pos 2)
        i=2: i < 2? NO → stop
        current is now pointing to node with data 20

Step 2: current is not NULL, so position is valid

Step 3: newNode(25)->next = current->next (which is node 30)
        current->next = newNode(25)

AFTER: head -> [10] -> [20] -> [25] -> [30] -> [40] -> NULL
```

---

### 2.3.4 — Insert AFTER a Given Node (by value)

```c
void insertAfterValue(Node *head, int target, int data) {
    // Step 1: Find the node with the target value
    Node *current = head;
    while (current != NULL && current->data != target) {
        current = current->next;
    }
    
    // Step 2: If target not found
    if (current == NULL) {
        printf("Value %d not found in the list!\n", target);
        return;
    }
    
    // Step 3: Create new node and insert after current
    Node *newNode = createNode(data);
    newNode->next = current->next;
    current->next = newNode;
}

// Usage: insertAfterValue(head, 20, 25);
// Inserts 25 after the node containing 20
```

---

## 2.4 — DELETION (4 Variations)

### 2.4.1 — Delete from the BEGINNING

```c
Node* deleteFromBeginning(Node *head) {
    // Step 1: Check if list is empty
    if (head == NULL) {
        printf("List is empty! Nothing to delete.\n");
        return NULL;
    }
    
    // Step 2: Save the current head (we need to free it later)
    Node *temp = head;
    
    // Step 3: Move head to the second node
    head = head->next;
    
    // Step 4: Free the old head node's memory
    printf("Deleted: %d\n", temp->data);
    free(temp);
    
    return head;
}
```

**Visual:**
```
BEFORE: head -> [10] -> [20] -> [30] -> NULL

Step 2: temp = head (temp points to node 10)
Step 3: head = head->next (head now points to node 20)
Step 4: free(temp) (node 10 is removed from memory)

AFTER: head -> [20] -> [30] -> NULL
```

**⚠️ Why do we need `temp`?**
If we do `head = head->next` first, we lose the reference to the first node — we can't free it anymore = **MEMORY LEAK**!

---

### 2.4.2 — Delete from the END

```c
Node* deleteFromEnd(Node *head) {
    // Case 1: Empty list
    if (head == NULL) {
        printf("List is empty!\n");
        return NULL;
    }
    
    // Case 2: Only one node in the list
    if (head->next == NULL) {
        printf("Deleted: %d\n", head->data);
        free(head);
        return NULL;  // List is now empty
    }
    
    // Case 3: Multiple nodes — find the SECOND-TO-LAST node
    Node *current = head;
    while (current->next->next != NULL) {  // Stop when next node is the last
        current = current->next;
    }
    
    // current is now the second-to-last node
    printf("Deleted: %d\n", current->next->data);
    free(current->next);      // Free the last node
    current->next = NULL;     // Second-to-last is now the last
    
    return head;
}
```

**Visual:**
```
BEFORE: head -> [10] -> [20] -> [30] -> NULL

Find second-to-last:
  current = 10: current->next->next = 30->next = NULL? NO
  Oh wait, current->next->next = 20->next->next... let me redo:
  
  current = 10: 10->next(20)->next(30) != NULL → move
  current = 20: 20->next(30)->next(NULL) == NULL → STOP!

current is node 20.
Free current->next (node 30).
Set current->next = NULL.

AFTER: head -> [10] -> [20] -> NULL
```

---

### 2.4.3 — Delete at a SPECIFIC POSITION

```c
Node* deleteAtPosition(Node *head, int position) {
    if (head == NULL) {
        printf("List is empty!\n");
        return NULL;
    }
    
    if (position < 1) {
        printf("Invalid position!\n");
        return head;
    }
    
    // Delete first node
    if (position == 1) {
        return deleteFromBeginning(head);
    }
    
    // Traverse to node just BEFORE the one to delete
    Node *current = head;
    for (int i = 1; i < position - 1 && current->next != NULL; i++) {
        current = current->next;
    }
    
    // Check if position is valid
    if (current->next == NULL) {
        printf("Position %d doesn't exist!\n", position);
        return head;
    }
    
    // Delete the node at position
    Node *temp = current->next;          // Node to be deleted
    current->next = current->next->next; // Skip over the deleted node
    printf("Deleted: %d\n", temp->data);
    free(temp);                          // Free memory
    
    return head;
}
```

**Visual: Delete node at position 3**
```
BEFORE: head -> [10] -> [20] -> [30] -> [40] -> NULL
                 pos1    pos2    pos3    pos4

Traverse to pos 2 (node before target):
  current = 10 (pos 1) → move → current = 20 (pos 2) → stop

temp = current->next = node(30)
current->next = current->next->next = node(40)
free(temp)

AFTER: head -> [10] -> [20] -> [40] -> NULL
```

---

### 2.4.4 — Delete by VALUE

```c
Node* deleteByValue(Node *head, int target) {
    if (head == NULL) {
        printf("List is empty!\n");
        return NULL;
    }
    
    // Special case: target is in the head node
    if (head->data == target) {
        Node *temp = head;
        head = head->next;
        free(temp);
        printf("Deleted node with value: %d\n", target);
        return head;
    }
    
    // Find the node BEFORE the target node
    Node *current = head;
    while (current->next != NULL && current->next->data != target) {
        current = current->next;
    }
    
    // If target was not found
    if (current->next == NULL) {
        printf("Value %d not found!\n", target);
        return head;
    }
    
    // Delete the target node
    Node *temp = current->next;
    current->next = current->next->next;
    free(temp);
    printf("Deleted node with value: %d\n", target);
    
    return head;
}
```

---

## 2.5 — SEARCHING

### 2.5.1 — Linear Search (Iterative)

```c
// Returns the position (1-indexed) if found, -1 if not found
int searchIterative(Node *head, int target) {
    Node *current = head;
    int position = 1;
    
    while (current != NULL) {
        if (current->data == target) {
            return position;  // Found!
        }
        current = current->next;
        position++;
    }
    
    return -1;  // Not found
}
```

### 2.5.2 — Linear Search (Recursive)

```c
int searchRecursive(Node *head, int target) {
    // Base case 1: Reached end, not found
    if (head == NULL) return 0;  // 0 = false
    
    // Base case 2: Found it!
    if (head->data == target) return 1;  // 1 = true
    
    // Recursive case: Search in the rest of the list
    return searchRecursive(head->next, target);
}
```

---

## 2.6 — COUNTING NODES

### Iterative
```c
int countNodes(Node *head) {
    int count = 0;
    Node *current = head;
    
    while (current != NULL) {
        count++;
        current = current->next;
    }
    
    return count;
}
```

### Recursive
```c
int countNodesRecursive(Node *head) {
    if (head == NULL) return 0;                    // Base case: empty = 0 nodes
    return 1 + countNodesRecursive(head->next);    // 1 (this node) + count of rest
}
```

---

## 2.7 — getNth NODE (Get data at position n)

```c
int getNth(Node *head, int n) {
    Node *current = head;
    int count = 1;
    
    while (current != NULL) {
        if (count == n) {
            return current->data;
        }
        count++;
        current = current->next;
    }
    
    printf("Position %d doesn't exist!\n", n);
    return -1;  // Error value
}
```

---

## 2.8 — COMPLETE DRIVER PROGRAM (Test Everything)

```c
#include <stdio.h>
#include <stdlib.h>

// ... (include all the functions above) ...

int main() {
    Node *head = NULL;  // Start with empty list
    
    // Build list: 10 -> 20 -> 30 -> 40 -> 50
    head = insertAtEnd(head, 10);
    head = insertAtEnd(head, 20);
    head = insertAtEnd(head, 30);
    head = insertAtEnd(head, 40);
    head = insertAtEnd(head, 50);
    printf("Initial list: ");
    printList(head);
    // Output: 10 -> 20 -> 30 -> 40 -> 50 -> NULL
    
    // Insert 5 at beginning
    head = insertAtBeginning(head, 5);
    printf("After insert 5 at beginning: ");
    printList(head);
    // Output: 5 -> 10 -> 20 -> 30 -> 40 -> 50 -> NULL
    
    // Insert 25 at position 4
    head = insertAtPosition(head, 25, 4);
    printf("After insert 25 at pos 4: ");
    printList(head);
    // Output: 5 -> 10 -> 20 -> 25 -> 30 -> 40 -> 50 -> NULL
    
    // Delete from beginning
    head = deleteFromBeginning(head);
    printf("After delete from beginning: ");
    printList(head);
    // Output: 10 -> 20 -> 25 -> 30 -> 40 -> 50 -> NULL
    
    // Delete from end
    head = deleteFromEnd(head);
    printf("After delete from end: ");
    printList(head);
    // Output: 10 -> 20 -> 25 -> 30 -> 40 -> NULL
    
    // Delete by value 25
    head = deleteByValue(head, 25);
    printf("After delete value 25: ");
    printList(head);
    // Output: 10 -> 20 -> 30 -> 40 -> NULL
    
    // Search
    int pos = searchIterative(head, 30);
    printf("Value 30 found at position: %d\n", pos);  // Output: 3
    
    // Count
    printf("Total nodes: %d\n", countNodes(head));  // Output: 4
    
    return 0;
}
```

---

> **🎯 End of Part 2: You now know ALL basic Singly Linked List operations — Create, Traverse, Insert (4 ways), Delete (4 ways), Search, Count. Next: Advanced operations!**
