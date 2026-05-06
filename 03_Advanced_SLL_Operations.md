# 📘 PART 3: ADVANCED SINGLY LINKED LIST OPERATIONS

---

## 3.1 — REVERSE a Linked List

### Iterative Approach (Most Important — Asked in every exam/interview!)

**Concept:** We use three pointers: `prev`, `current`, and `nextNode`. We walk through the list, flipping each pointer backward.

```c
Node* reverseList(Node *head) {
    Node *prev = NULL;       // Will become the new head eventually
    Node *current = head;    // Start at the head
    Node *nextNode = NULL;   // Temporary storage
    
    while (current != NULL) {
        // Step 1: Save the next node (before we break the link)
        nextNode = current->next;
        
        // Step 2: REVERSE the link — point current node backward
        current->next = prev;
        
        // Step 3: Move prev and current one step forward
        prev = current;
        current = nextNode;
    }
    
    // When loop ends, current is NULL and prev is at the last node
    // prev is the new head
    return prev;
}
```

**Step-by-step visual for list: 10 -> 20 -> 30 -> NULL**
```
Initial:  prev=NULL  current=10  nextNode=NULL

--- Iteration 1 ---
nextNode = 10->next = 20              Save next
10->next = NULL (prev)                Reverse link: 10 now points to NULL
prev = 10                             Move prev forward
current = 20                          Move current forward
State: NULL <- 10   20 -> 30 -> NULL
       prev        curr

--- Iteration 2 ---
nextNode = 20->next = 30
20->next = 10 (prev)                  Reverse link: 20 now points to 10
prev = 20
current = 30
State: NULL <- 10 <- 20   30 -> NULL
                    prev  curr

--- Iteration 3 ---
nextNode = 30->next = NULL
30->next = 20 (prev)                  Reverse link: 30 now points to 20
prev = 30
current = NULL                        Loop ends!
State: NULL <- 10 <- 20 <- 30
                           prev  curr=NULL

Return prev (30) as new head.
Result: 30 -> 20 -> 10 -> NULL  ✅
```

### Recursive Approach

```c
Node* reverseRecursive(Node *head) {
    // Base case: empty list or single node
    if (head == NULL || head->next == NULL) {
        return head;
    }
    
    // Recursively reverse the rest of the list
    Node *newHead = reverseRecursive(head->next);
    
    // Make the next node point back to current node
    head->next->next = head;
    
    // Remove the old forward link
    head->next = NULL;
    
    return newHead;
}
```

**How the recursion works for 10 -> 20 -> 30 -> NULL:**
```
Call 1: reverseRecursive(10)
  Call 2: reverseRecursive(20)
    Call 3: reverseRecursive(30)
      30->next is NULL → return 30  (base case)
    Back in Call 2: newHead = 30
      head=20, head->next=30, so 30->next = 20  (20's next node now points BACK to 20)
      20->next = NULL
      return 30
  Back in Call 1: newHead = 30
    head=10, head->next=20, so 20->next = 10
    10->next = NULL
    return 30

Result: 30 -> 20 -> 10 -> NULL  ✅
```

---

## 3.2 — Find the MIDDLE Node

### Two-Pointer (Slow-Fast / Tortoise-Hare) Approach

**Concept:** Use two pointers — `slow` moves 1 step, `fast` moves 2 steps. When `fast` reaches the end, `slow` is at the middle!

```c
Node* findMiddle(Node *head) {
    if (head == NULL) return NULL;
    
    Node *slow = head;  // Moves 1 step at a time
    Node *fast = head;  // Moves 2 steps at a time
    
    while (fast != NULL && fast->next != NULL) {
        slow = slow->next;           // 1 step
        fast = fast->next->next;     // 2 steps
    }
    
    // When fast reaches end, slow is at middle
    return slow;
}

// Usage:
// Node *mid = findMiddle(head);
// printf("Middle: %d\n", mid->data);
```

**Visual for list: 10 -> 20 -> 30 -> 40 -> 50 -> NULL**
```
Start:      slow=10, fast=10
Iteration 1: slow=20, fast=30
Iteration 2: slow=30, fast=50
Iteration 3: fast->next = NULL → STOP

Middle = slow = 30 ✅

(For even-length list 10->20->30->40, the "middle" returned is the 2nd of the two middle nodes, i.e., 30)
```

**Why this works:** Fast moves at 2x speed. When fast finishes the full distance, slow has covered exactly half. It's like two runners — one runs twice as fast, when the fast one finishes the race, the slow one is at the halfway point!

---

## 3.3 — Detect a CYCLE (Loop) in a Linked List

### What is a cycle?
Normally: 10 -> 20 -> 30 -> NULL (ends at NULL)
With cycle: 10 -> 20 -> 30 -> 20 (30 points BACK to 20 — infinite loop!)

### Floyd's Cycle Detection (Tortoise and Hare)

```c
int hasCycle(Node *head) {
    Node *slow = head;
    Node *fast = head;
    
    while (fast != NULL && fast->next != NULL) {
        slow = slow->next;           // 1 step
        fast = fast->next->next;     // 2 steps
        
        // If they meet, there's a cycle!
        if (slow == fast) {
            return 1;  // Cycle detected
        }
    }
    
    // fast reached NULL — no cycle
    return 0;
}
```

**Why this works:** If there's a cycle, fast will eventually "lap" slow and they'll meet (like two runners on a circular track). If there's no cycle, fast reaches NULL.

### Finding WHERE the Cycle Starts

```c
Node* findCycleStart(Node *head) {
    Node *slow = head, *fast = head;
    
    // Step 1: Detect cycle
    while (fast != NULL && fast->next != NULL) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) break;
    }
    
    // No cycle
    if (fast == NULL || fast->next == NULL) return NULL;
    
    // Step 2: Find the start of the cycle
    // Reset slow to head, keep fast at meeting point
    slow = head;
    while (slow != fast) {
        slow = slow->next;
        fast = fast->next;  // Both move 1 step now
    }
    
    return slow;  // This is where the cycle starts
}
```

---

## 3.4 — Find Nth Node from the END

### Two-Pointer Approach

**Concept:** Move `first` pointer N steps ahead, then move both `first` and `second` together. When `first` reaches NULL, `second` is at the Nth from end.

```c
Node* getNthFromEnd(Node *head, int n) {
    Node *first = head;
    Node *second = head;
    
    // Step 1: Move first pointer n steps ahead
    for (int i = 0; i < n; i++) {
        if (first == NULL) {
            printf("List has fewer than %d nodes!\n", n);
            return NULL;
        }
        first = first->next;
    }
    
    // Step 2: Move both pointers together until first reaches NULL
    while (first != NULL) {
        first = first->next;
        second = second->next;
    }
    
    return second;  // second is now at nth node from end
}
```

**Visual: Find 2nd node from end in 10 -> 20 -> 30 -> 40 -> 50**
```
Move first 2 steps: first=30, second=10
Move together:
  first=40, second=20
  first=50, second=30
  first=NULL, second=40 → STOP

2nd from end = 40 ✅
```

---

## 3.5 — Remove DUPLICATES from a Sorted List

```c
void removeDuplicatesSorted(Node *head) {
    Node *current = head;
    
    while (current != NULL && current->next != NULL) {
        if (current->data == current->next->data) {
            // Duplicate found — remove the next node
            Node *duplicate = current->next;
            current->next = current->next->next;
            free(duplicate);
            // DON'T move current — there might be more duplicates
        } else {
            current = current->next;  // Move only if no duplicate
        }
    }
}
```

---

## 3.6 — MERGE Two Sorted Lists

```c
Node* mergeSortedLists(Node *a, Node *b) {
    // Base cases
    if (a == NULL) return b;
    if (b == NULL) return a;
    
    Node *result = NULL;
    
    // Pick the smaller value
    if (a->data <= b->data) {
        result = a;
        result->next = mergeSortedLists(a->next, b);
    } else {
        result = b;
        result->next = mergeSortedLists(a, b->next);
    }
    
    return result;
}
```

---

## 3.7 — SORT a Linked List (Bubble Sort)

```c
void bubbleSortList(Node *head) {
    if (head == NULL) return;
    
    int swapped;
    Node *current;
    Node *last = NULL;  // Optimization: last sorted node
    
    do {
        swapped = 0;
        current = head;
        
        while (current->next != last) {
            if (current->data > current->next->data) {
                // Swap DATA (not nodes — easier!)
                int temp = current->data;
                current->data = current->next->data;
                current->next->data = temp;
                swapped = 1;
            }
            current = current->next;
        }
        last = current;
    } while (swapped);
}
```

---

## 3.8 — Check if List is a PALINDROME

**Approach:** Find middle, reverse second half, compare both halves.

```c
int isPalindrome(Node *head) {
    if (head == NULL || head->next == NULL) return 1;
    
    // Step 1: Find middle
    Node *slow = head, *fast = head;
    while (fast->next != NULL && fast->next->next != NULL) {
        slow = slow->next;
        fast = fast->next->next;
    }
    
    // Step 2: Reverse second half
    Node *secondHalf = reverseList(slow->next);
    
    // Step 3: Compare both halves
    Node *firstHalf = head;
    Node *temp = secondHalf;  // Save for restoring later
    int result = 1;
    
    while (secondHalf != NULL) {
        if (firstHalf->data != secondHalf->data) {
            result = 0;
            break;
        }
        firstHalf = firstHalf->next;
        secondHalf = secondHalf->next;
    }
    
    // Step 4: Restore the list (optional but good practice)
    slow->next = reverseList(temp);
    
    return result;
}
```

---

## 3.9 — Find INTERSECTION Point of Two Lists

```c
Node* findIntersection(Node *headA, Node *headB) {
    // Step 1: Get lengths
    int lenA = countNodes(headA);
    int lenB = countNodes(headB);
    
    // Step 2: Advance the longer list by the difference
    int diff = lenA - lenB;
    Node *a = headA, *b = headB;
    
    if (diff > 0) {
        for (int i = 0; i < diff; i++) a = a->next;
    } else {
        for (int i = 0; i < -diff; i++) b = b->next;
    }
    
    // Step 3: Move both pointers until they meet
    while (a != NULL && b != NULL) {
        if (a == b) return a;  // Intersection point!
        a = a->next;
        b = b->next;
    }
    
    return NULL;  // No intersection
}
```

---

## 3.10 — FREE the Entire List

**Always free your list when done to prevent memory leaks!**

```c
void freeList(Node *head) {
    Node *current = head;
    Node *nextNode;
    
    while (current != NULL) {
        nextNode = current->next;  // Save next before freeing
        free(current);             // Free current node
        current = nextNode;        // Move to next
    }
}
```

**⚠️ Common mistake: `free(head)` does NOT free the whole list — it only frees ONE node!**

---

> **🎯 End of Part 3: You now know Reverse (iterative + recursive), Find Middle, Detect Cycle, Nth from End, Remove Duplicates, Merge, Sort, Palindrome Check, Intersection, and Free. Next: Doubly Linked Lists!**
