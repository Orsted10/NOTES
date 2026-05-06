# 📘 PART 8A: CODING QUESTIONS WITH FULL SOLUTIONS (Problems 1–15)

> Every solution includes: Problem statement, Approach, Full Code, Line-by-line explanation, and Sample Output.

---

## PROBLEM 1: Create and Display a Linked List

**Problem:** Create a linked list of n elements and display them.

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node *next;
} Node;

// Creates a new node
Node* createNode(int data) {
    Node *newNode = (Node *)malloc(sizeof(Node));
    newNode->data = data;
    newNode->next = NULL;
    return newNode;
}

// Displays the list
void display(Node *head) {
    Node *temp = head;
    while (temp != NULL) {
        printf("%d -> ", temp->data);
        temp = temp->next;
    }
    printf("NULL\n");
}

int main() {
    int n, value;
    Node *head = NULL, *tail = NULL;

    printf("Enter number of elements: ");
    scanf("%d", &n);

    for (int i = 0; i < n; i++) {
        printf("Enter element %d: ", i + 1);
        scanf("%d", &value);

        Node *newNode = createNode(value);

        if (head == NULL) {
            head = newNode;   // First node becomes head
            tail = newNode;   // Also the tail
        } else {
            tail->next = newNode;  // Add after current tail
            tail = newNode;        // Update tail
        }
    }

    printf("Linked List: ");
    display(head);

    return 0;
}
```

**Output:**
```
Enter number of elements: 4
Enter element 1: 10
Enter element 2: 20
Enter element 3: 30
Enter element 4: 40
Linked List: 10 -> 20 -> 30 -> 40 -> NULL
```

---

## PROBLEM 2: Insert at Beginning, End, and Specific Position

**Problem:** Menu-driven program to insert at beginning, end, or a position.

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node { int data; struct Node *next; } Node;

Node* createNode(int data) {
    Node *n = (Node *)malloc(sizeof(Node));
    n->data = data; n->next = NULL;
    return n;
}

void display(Node *head) {
    Node *t = head;
    while (t) { printf("%d -> ", t->data); t = t->next; }
    printf("NULL\n");
}

// Insert at beginning
Node* insertBeg(Node *head, int data) {
    Node *n = createNode(data);
    n->next = head;      // new node points to old head
    return n;            // new node IS the new head
}

// Insert at end
Node* insertEnd(Node *head, int data) {
    Node *n = createNode(data);
    if (!head) return n; // empty list — new node is head
    Node *t = head;
    while (t->next) t = t->next;  // go to last node
    t->next = n;                   // last node now points to new node
    return head;
}

// Insert at position (1-indexed)
Node* insertPos(Node *head, int data, int pos) {
    if (pos == 1) return insertBeg(head, data);
    
    Node *n = createNode(data);
    Node *t = head;
    
    // Go to node at (pos - 1)
    for (int i = 1; i < pos - 1 && t != NULL; i++)
        t = t->next;
    
    if (t == NULL) { printf("Invalid position!\n"); free(n); return head; }
    
    n->next = t->next;   // new node points to what was at pos
    t->next = n;          // node at (pos-1) points to new node
    return head;
}

int main() {
    Node *head = NULL;
    int choice, val, pos;

    while (1) {
        printf("\n1.Insert Beginning  2.Insert End  3.Insert Position  4.Display  5.Exit\nChoice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: printf("Value: "); scanf("%d", &val);
                    head = insertBeg(head, val); break;
            case 2: printf("Value: "); scanf("%d", &val);
                    head = insertEnd(head, val); break;
            case 3: printf("Value and Position: "); scanf("%d %d", &val, &pos);
                    head = insertPos(head, val, pos); break;
            case 4: display(head); break;
            case 5: return 0;
        }
    }
}
```

---

## PROBLEM 3: Delete from Beginning, End, and by Value

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node { int data; struct Node *next; } Node;

Node* createNode(int d) { Node *n=(Node*)malloc(sizeof(Node)); n->data=d; n->next=NULL; return n; }

void display(Node *h) { while(h){printf("%d -> ",h->data);h=h->next;} printf("NULL\n"); }

Node* deleteBeg(Node *head) {
    if (!head) { printf("Empty!\n"); return NULL; }
    Node *temp = head;          // save first node
    head = head->next;          // move head forward
    printf("Deleted: %d\n", temp->data);
    free(temp);                 // free old head
    return head;
}

Node* deleteEnd(Node *head) {
    if (!head) { printf("Empty!\n"); return NULL; }
    if (!head->next) {          // only one node
        printf("Deleted: %d\n", head->data);
        free(head);
        return NULL;
    }
    Node *t = head;
    while (t->next->next) t = t->next;  // find second-to-last
    printf("Deleted: %d\n", t->next->data);
    free(t->next);              // free last node
    t->next = NULL;             // second-to-last becomes last
    return head;
}

Node* deleteVal(Node *head, int val) {
    if (!head) { printf("Empty!\n"); return NULL; }
    
    // If head has the value
    if (head->data == val) {
        Node *temp = head;
        head = head->next;
        free(temp);
        return head;
    }
    
    // Find node BEFORE the target
    Node *t = head;
    while (t->next && t->next->data != val) t = t->next;
    
    if (!t->next) { printf("Not found!\n"); return head; }
    
    Node *temp = t->next;       // target node
    t->next = temp->next;       // bypass target
    printf("Deleted: %d\n", temp->data);
    free(temp);
    return head;
}

int main() {
    Node *head = NULL;
    // Build list: 10->20->30->40->50
    for (int i = 5; i >= 1; i--) head = createNode(i * 10), head->next = (i < 5) ? head->next : NULL;
    // Simpler build:
    head = NULL;
    for (int v = 10; v <= 50; v += 10) {
        Node *n = createNode(v);
        if (!head) { head = n; continue; }
        Node *t = head; while(t->next) t = t->next; t->next = n;
    }

    printf("Original: "); display(head);

    head = deleteBeg(head);
    printf("After delete beginning: "); display(head);

    head = deleteEnd(head);
    printf("After delete end: "); display(head);

    head = deleteVal(head, 30);
    printf("After delete value 30: "); display(head);

    return 0;
}
```

**Output:**
```
Original: 10 -> 20 -> 30 -> 40 -> 50 -> NULL
Deleted: 10
After delete beginning: 20 -> 30 -> 40 -> 50 -> NULL
Deleted: 50
After delete end: 20 -> 30 -> 40 -> NULL
Deleted: 30
After delete value 30: 20 -> 40 -> NULL
```

---

## PROBLEM 4: Reverse a Linked List (Iterative)

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node { int data; struct Node *next; } Node;

Node* createNode(int d) { Node *n=(Node*)malloc(sizeof(Node)); n->data=d; n->next=NULL; return n; }
void display(Node *h) { while(h){printf("%d -> ",h->data);h=h->next;} printf("NULL\n"); }

Node* reverse(Node *head) {
    Node *prev = NULL;      // will become the new head
    Node *current = head;   // starts at old head
    Node *nextNode = NULL;  // temporary storage

    while (current != NULL) {
        nextNode = current->next;    // SAVE next (before we break the link)
        current->next = prev;        // REVERSE the link
        prev = current;              // ADVANCE prev
        current = nextNode;          // ADVANCE current
    }

    return prev;  // prev is now at the last node = new head
}

int main() {
    Node *head = NULL;
    for (int v = 10; v <= 50; v += 10) {
        Node *n = createNode(v);
        if (!head) { head = n; } 
        else { Node *t = head; while(t->next) t = t->next; t->next = n; }
    }

    printf("Original: "); display(head);
    head = reverse(head);
    printf("Reversed: "); display(head);

    return 0;
}
```

**Output:**
```
Original: 10 -> 20 -> 30 -> 40 -> 50 -> NULL
Reversed: 50 -> 40 -> 30 -> 20 -> 10 -> NULL
```

---

## PROBLEM 5: Reverse a Linked List (Recursive)

```c
Node* reverseRecursive(Node *head) {
    // Base case: empty or single node
    if (head == NULL || head->next == NULL)
        return head;

    // Reverse the rest of the list
    Node *newHead = reverseRecursive(head->next);

    // Make the node after head point BACK to head
    head->next->next = head;

    // Remove the old forward link
    head->next = NULL;

    return newHead;  // newHead stays the same through all calls
}
```

**How it unwinds for 1->2->3->NULL:**
```
Call reverseRecursive(1):
  Call reverseRecursive(2):
    Call reverseRecursive(3):
      3->next is NULL → return 3  (base case)
    newHead = 3
    2->next(=3)->next = 2  → now 3->2
    2->next = NULL         → now 3->2->NULL
    return 3
  newHead = 3
  1->next(=2)->next = 1   → now 3->2->1
  1->next = NULL           → now 3->2->1->NULL
  return 3

Result: 3 -> 2 -> 1 -> NULL ✅
```

---

## PROBLEM 6: Find Middle Element

```c
void findMiddle(Node *head) {
    if (!head) { printf("Empty list!\n"); return; }

    Node *slow = head;   // moves 1 step
    Node *fast = head;   // moves 2 steps

    while (fast != NULL && fast->next != NULL) {
        slow = slow->next;
        fast = fast->next->next;
    }

    printf("Middle element: %d\n", slow->data);
}
```

---

## PROBLEM 7: Detect a Cycle

```c
int detectCycle(Node *head) {
    Node *slow = head, *fast = head;

    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;

        if (slow == fast) {
            printf("Cycle detected!\n");
            return 1;
        }
    }

    printf("No cycle.\n");
    return 0;
}

// To test, manually create a cycle:
// Node *a=createNode(1), *b=createNode(2), *c=createNode(3);
// a->next=b; b->next=c; c->next=b; // cycle: c -> b -> c -> b...
// detectCycle(a);  // Output: Cycle detected!
```

---

## PROBLEM 8: Find Nth Node from End

```c
void nthFromEnd(Node *head, int n) {
    Node *first = head, *second = head;

    // Move first n steps ahead
    for (int i = 0; i < n; i++) {
        if (!first) { printf("List too short!\n"); return; }
        first = first->next;
    }

    // Move both until first reaches NULL
    while (first) {
        first = first->next;
        second = second->next;
    }

    printf("%dth from end: %d\n", n, second->data);
}

// For 10->20->30->40->50, nthFromEnd(head, 2) gives 40
```

---

## PROBLEM 9: Remove Duplicates from Sorted List

```c
void removeDupsSorted(Node *head) {
    Node *current = head;

    while (current && current->next) {
        if (current->data == current->next->data) {
            Node *dup = current->next;         // save duplicate
            current->next = dup->next;         // skip over it
            free(dup);                         // free it
            // DON'T advance — check for more duplicates
        } else {
            current = current->next;           // advance only if no dup
        }
    }
}

// Input:  1->1->2->3->3->NULL
// Output: 1->2->3->NULL
```

---

## PROBLEM 10: Merge Two Sorted Lists

```c
Node* mergeSorted(Node *a, Node *b) {
    // Base cases
    if (!a) return b;
    if (!b) return a;

    Node *result;

    if (a->data <= b->data) {
        result = a;                              // pick a
        result->next = mergeSorted(a->next, b);  // merge rest
    } else {
        result = b;                              // pick b
        result->next = mergeSorted(a, b->next);  // merge rest
    }

    return result;
}

// List1: 1->3->5  List2: 2->4->6
// Merged: 1->2->3->4->5->6
```

---

## PROBLEM 11: Check if Palindrome

```c
Node* reverseList(Node *head) {
    Node *prev=NULL, *curr=head, *next;
    while(curr) { next=curr->next; curr->next=prev; prev=curr; curr=next; }
    return prev;
}

int isPalindrome(Node *head) {
    if (!head || !head->next) return 1;

    // Find middle
    Node *slow=head, *fast=head;
    while (fast->next && fast->next->next) {
        slow = slow->next;
        fast = fast->next->next;
    }

    // Reverse second half
    Node *secondHalf = reverseList(slow->next);
    Node *firstHalf = head;

    // Compare
    Node *temp = secondHalf;
    while (temp) {
        if (firstHalf->data != temp->data) {
            slow->next = reverseList(secondHalf); // restore
            return 0;  // NOT palindrome
        }
        firstHalf = firstHalf->next;
        temp = temp->next;
    }

    slow->next = reverseList(secondHalf); // restore
    return 1;  // IS palindrome
}

// 1->2->3->2->1 → Palindrome ✅
// 1->2->3->4->5 → Not palindrome ❌
```

---

## PROBLEM 12: Sort a Linked List (Bubble Sort)

```c
void bubbleSort(Node *head) {
    if (!head) return;

    int swapped;
    Node *ptr, *last = NULL;

    do {
        swapped = 0;
        ptr = head;

        while (ptr->next != last) {
            if (ptr->data > ptr->next->data) {
                // Swap data (not pointers — easier)
                int temp = ptr->data;
                ptr->data = ptr->next->data;
                ptr->next->data = temp;
                swapped = 1;
            }
            ptr = ptr->next;
        }
        last = ptr;  // last element is now sorted
    } while (swapped);
}

// Input:  40->10->30->20->NULL
// Output: 10->20->30->40->NULL
```

---

## PROBLEM 13: Count Occurrences of a Value

```c
int countOccurrences(Node *head, int key) {
    int count = 0;
    Node *current = head;

    while (current) {
        if (current->data == key)
            count++;
        current = current->next;
    }

    return count;
}

// In list 1->2->3->2->4->2, countOccurrences(head, 2) returns 3
```

---

## PROBLEM 14: Swap Two Nodes (Without Swapping Data)

**This is a classic tricky problem!**

```c
Node* swapNodes(Node *head, int x, int y) {
    if (x == y) return head;

    // Find x and its previous node
    Node *prevX = NULL, *currX = head;
    while (currX && currX->data != x) {
        prevX = currX;
        currX = currX->next;
    }

    // Find y and its previous node
    Node *prevY = NULL, *currY = head;
    while (currY && currY->data != y) {
        prevY = currY;
        currY = currY->next;
    }

    // If either not found, can't swap
    if (!currX || !currY) return head;

    // If x is head
    if (prevX) prevX->next = currY;
    else head = currY;

    // If y is head
    if (prevY) prevY->next = currX;
    else head = currX;

    // Swap next pointers
    Node *temp = currX->next;
    currX->next = currY->next;
    currY->next = temp;

    return head;
}
```

---

## PROBLEM 15: Intersection Point of Two Lists

```c
int getLength(Node *head) {
    int len = 0;
    while (head) { len++; head = head->next; }
    return len;
}

Node* findIntersection(Node *headA, Node *headB) {
    int lenA = getLength(headA);
    int lenB = getLength(headB);

    // Advance the longer list
    int diff = lenA - lenB;
    Node *a = headA, *b = headB;

    if (diff > 0) while(diff--) a = a->next;
    else while(diff++) b = b->next;

    // Move together until they meet
    while (a && b) {
        if (a == b) return a;  // intersection!
        a = a->next;
        b = b->next;
    }

    return NULL;
}
```

---

> **Continued in Part 8B with Problems 16–30...**
