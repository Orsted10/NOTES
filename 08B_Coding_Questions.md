# 📘 PART 8B: CODING QUESTIONS WITH FULL SOLUTIONS (Problems 16–30)

---

## PROBLEM 16: Doubly Linked List — All Operations

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct DNode {
    int data;
    struct DNode *prev, *next;
} DNode;

DNode* createDNode(int d) {
    DNode *n = (DNode*)malloc(sizeof(DNode));
    n->data = d; n->prev = n->next = NULL;
    return n;
}

DNode* dllInsertEnd(DNode *head, int data) {
    DNode *n = createDNode(data);
    if (!head) return n;
    DNode *t = head;
    while (t->next) t = t->next;
    t->next = n;
    n->prev = t;
    return head;
}

DNode* dllDeleteVal(DNode *head, int val) {
    DNode *t = head;
    while (t && t->data != val) t = t->next;
    if (!t) { printf("Not found!\n"); return head; }
    
    if (t->prev) t->prev->next = t->next;  // bypass forward
    else head = t->next;                     // deleting head
    
    if (t->next) t->next->prev = t->prev;  // bypass backward
    
    free(t);
    return head;
}

void dllDisplayForward(DNode *h) {
    printf("Forward:  ");
    while (h) { printf("%d <-> ", h->data); h = h->next; }
    printf("NULL\n");
}

void dllDisplayBackward(DNode *h) {
    if (!h) return;
    while (h->next) h = h->next; // go to last
    printf("Backward: ");
    while (h) { printf("%d <-> ", h->data); h = h->prev; }
    printf("NULL\n");
}

int main() {
    DNode *head = NULL;
    head = dllInsertEnd(head, 10);
    head = dllInsertEnd(head, 20);
    head = dllInsertEnd(head, 30);
    head = dllInsertEnd(head, 40);

    dllDisplayForward(head);   // 10 <-> 20 <-> 30 <-> 40 <-> NULL
    dllDisplayBackward(head);  // 40 <-> 30 <-> 20 <-> 10 <-> NULL

    head = dllDeleteVal(head, 20);
    dllDisplayForward(head);   // 10 <-> 30 <-> 40 <-> NULL
    return 0;
}
```

---

## PROBLEM 17: Circular Linked List — Create, Display, Insert

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct CNode { int data; struct CNode *next; } CNode;

CNode* csllInsertEnd(CNode *head, int data) {
    CNode *n = (CNode*)malloc(sizeof(CNode));
    n->data = data;

    if (!head) {
        n->next = n;  // single node points to itself
        return n;
    }

    CNode *t = head;
    while (t->next != head) t = t->next;  // find last
    t->next = n;     // old last -> new node
    n->next = head;  // new node -> head (circular!)
    return head;
}

void csllDisplay(CNode *head) {
    if (!head) { printf("Empty\n"); return; }
    CNode *t = head;
    do {
        printf("%d -> ", t->data);
        t = t->next;
    } while (t != head);
    printf("(back to %d)\n", head->data);
}

int main() {
    CNode *head = NULL;
    head = csllInsertEnd(head, 10);
    head = csllInsertEnd(head, 20);
    head = csllInsertEnd(head, 30);
    csllDisplay(head);
    // Output: 10 -> 20 -> 30 -> (back to 10)
    return 0;
}
```

---

## PROBLEM 18: Stack Implementation Using Linked List

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node { int data; struct Node *next; } Node;

Node *top = NULL;  // global stack top

void push(int data) {
    Node *n = (Node*)malloc(sizeof(Node));
    n->data = data;
    n->next = top;  // new node points to current top
    top = n;        // new node IS the new top
    printf("Pushed: %d\n", data);
}

int pop() {
    if (!top) { printf("Stack Underflow!\n"); return -1; }
    Node *temp = top;
    int val = temp->data;
    top = top->next;
    free(temp);
    printf("Popped: %d\n", val);
    return val;
}

int peek() {
    if (!top) { printf("Stack is empty!\n"); return -1; }
    return top->data;
}

void displayStack() {
    Node *t = top;
    printf("Stack (top to bottom): ");
    while (t) { printf("%d ", t->data); t = t->next; }
    printf("\n");
}

int main() {
    push(10); push(20); push(30);
    displayStack();       // 30 20 10
    pop();                // Popped: 30
    printf("Top: %d\n", peek());  // Top: 20
    displayStack();       // 20 10
    return 0;
}
```

---

## PROBLEM 19: Queue Implementation Using Linked List

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node { int data; struct Node *next; } Node;

Node *front = NULL, *rear = NULL;

void enqueue(int data) {
    Node *n = (Node*)malloc(sizeof(Node));
    n->data = data; n->next = NULL;

    if (!rear) {
        front = rear = n;  // first element
    } else {
        rear->next = n;    // add after current rear
        rear = n;          // update rear
    }
    printf("Enqueued: %d\n", data);
}

int dequeue() {
    if (!front) { printf("Queue Underflow!\n"); return -1; }
    Node *temp = front;
    int val = temp->data;
    front = front->next;

    if (!front) rear = NULL;  // queue became empty

    free(temp);
    printf("Dequeued: %d\n", val);
    return val;
}

void displayQueue() {
    Node *t = front;
    printf("Queue (front to rear): ");
    while (t) { printf("%d ", t->data); t = t->next; }
    printf("\n");
}

int main() {
    enqueue(10); enqueue(20); enqueue(30);
    displayQueue();  // 10 20 30
    dequeue();       // Dequeued: 10
    displayQueue();  // 20 30
    return 0;
}
```

---

## PROBLEM 20: Polynomial Addition Using Linked Lists

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Term {
    int coeff;  // coefficient
    int exp;    // exponent
    struct Term *next;
} Term;

Term* createTerm(int c, int e) {
    Term *t = (Term*)malloc(sizeof(Term));
    t->coeff = c; t->exp = e; t->next = NULL;
    return t;
}

void insertTerm(Term **poly, int c, int e) {
    Term *n = createTerm(c, e);
    if (!*poly) { *poly = n; return; }
    Term *t = *poly;
    while (t->next) t = t->next;
    t->next = n;
}

void displayPoly(Term *p) {
    while (p) {
        printf("%dx^%d", p->coeff, p->exp);
        p = p->next;
        if (p) printf(" + ");
    }
    printf("\n");
}

Term* addPolynomials(Term *a, Term *b) {
    Term *result = NULL;

    while (a && b) {
        if (a->exp > b->exp) {
            insertTerm(&result, a->coeff, a->exp);
            a = a->next;
        } else if (a->exp < b->exp) {
            insertTerm(&result, b->coeff, b->exp);
            b = b->next;
        } else {
            // Same exponent — add coefficients
            int sum = a->coeff + b->coeff;
            if (sum != 0) insertTerm(&result, sum, a->exp);
            a = a->next;
            b = b->next;
        }
    }

    // Add remaining terms
    while (a) { insertTerm(&result, a->coeff, a->exp); a = a->next; }
    while (b) { insertTerm(&result, b->coeff, b->exp); b = b->next; }

    return result;
}

int main() {
    // Poly A: 5x^3 + 4x^2 + 2x^0
    Term *a = NULL;
    insertTerm(&a, 5, 3);
    insertTerm(&a, 4, 2);
    insertTerm(&a, 2, 0);

    // Poly B: 3x^3 + 1x^1 + 6x^0
    Term *b = NULL;
    insertTerm(&b, 3, 3);
    insertTerm(&b, 1, 1);
    insertTerm(&b, 6, 0);

    printf("A = "); displayPoly(a);
    printf("B = "); displayPoly(b);

    Term *sum = addPolynomials(a, b);
    printf("A+B = "); displayPoly(sum);
    // Output: 8x^3 + 4x^2 + 1x^1 + 8x^0

    return 0;
}
```

---

## PROBLEM 21: Josephus Problem (Circular Linked List)

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct CNode { int data; struct CNode *next; } CNode;

void josephus(int n, int k) {
    // Create circular list of n people
    CNode *head = (CNode*)malloc(sizeof(CNode));
    head->data = 1;
    head->next = head;

    CNode *prev = head;
    for (int i = 2; i <= n; i++) {
        CNode *node = (CNode*)malloc(sizeof(CNode));
        node->data = i;
        node->next = head;
        prev->next = node;
        prev = node;
    }

    // Eliminate every kth person
    CNode *current = prev; // start just before head
    while (current->next != current) {
        // Count k steps
        for (int i = 0; i < k; i++)
            current = current->next;

        // Remove the kth node
        CNode *toRemove = current->next;
        printf("Eliminated: %d\n", toRemove->data);
        current->next = toRemove->next;
        free(toRemove);
    }

    printf("Survivor: %d\n", current->data);
    free(current);
}

int main() {
    printf("Josephus Problem (n=7, k=3):\n");
    josephus(7, 3);
    return 0;
}
```

---

## PROBLEM 22: Flatten a Multi-Level Linked List

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct MNode {
    int data;
    struct MNode *next;
    struct MNode *child;  // pointer to another list
} MNode;

// Flatten by appending child lists to the end
void flatten(MNode *head) {
    if (!head) return;

    MNode *tail = head;
    while (tail->next) tail = tail->next; // find tail

    MNode *current = head;
    while (current) {
        if (current->child) {
            // Append child list to tail
            tail->next = current->child;
            current->child = NULL;

            // Update tail
            while (tail->next) tail = tail->next;
        }
        current = current->next;
    }
}
```

---

## PROBLEM 23: Add Two Numbers (Each Digit is a Node)

**Problem:** 342 stored as 2->4->3, 465 stored as 5->6->4. Sum = 807 stored as 7->0->8.

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node { int data; struct Node *next; } Node;

Node* createNode(int d) { Node *n=(Node*)malloc(sizeof(Node)); n->data=d; n->next=NULL; return n; }

Node* addTwoNumbers(Node *l1, Node *l2) {
    Node dummy = {0, NULL};  // dummy head for easier code
    Node *tail = &dummy;
    int carry = 0;

    while (l1 || l2 || carry) {
        int sum = carry;
        if (l1) { sum += l1->data; l1 = l1->next; }
        if (l2) { sum += l2->data; l2 = l2->next; }

        carry = sum / 10;           // carry for next digit
        tail->next = createNode(sum % 10);  // current digit
        tail = tail->next;
    }

    return dummy.next;
}

void display(Node *h) { while(h){printf("%d -> ",h->data);h=h->next;} printf("NULL\n"); }

int main() {
    // 342 = 2->4->3
    Node *l1 = createNode(2); l1->next = createNode(4); l1->next->next = createNode(3);
    // 465 = 5->6->4
    Node *l2 = createNode(5); l2->next = createNode(6); l2->next->next = createNode(4);

    printf("L1: "); display(l1);
    printf("L2: "); display(l2);

    Node *sum = addTwoNumbers(l1, l2);
    printf("Sum: "); display(sum);
    // Output: 7 -> 0 -> 8 -> NULL  (342 + 465 = 807)

    return 0;
}
```

---

## PROBLEM 24: Rotate a Linked List by K positions

```c
Node* rotateRight(Node *head, int k) {
    if (!head || !head->next || k == 0) return head;

    // Find length and make circular
    int len = 1;
    Node *tail = head;
    while (tail->next) { len++; tail = tail->next; }

    k = k % len;  // handle k > length
    if (k == 0) return head;

    tail->next = head;  // make circular

    // Find new tail (len - k - 1 steps from head)
    int stepsToNewTail = len - k;
    Node *newTail = head;
    for (int i = 1; i < stepsToNewTail; i++)
        newTail = newTail->next;

    Node *newHead = newTail->next;
    newTail->next = NULL;  // break the circle

    return newHead;
}

// Input:  1->2->3->4->5, k=2
// Output: 4->5->1->2->3
```

---

## PROBLEM 25: Clone a Linked List with Random Pointer

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct RNode {
    int data;
    struct RNode *next;
    struct RNode *random;  // can point to ANY node in the list
} RNode;

RNode* cloneWithRandom(RNode *head) {
    if (!head) return NULL;

    // Step 1: Create copies interleaved: A->A'->B->B'->C->C'
    RNode *curr = head;
    while (curr) {
        RNode *copy = (RNode*)malloc(sizeof(RNode));
        copy->data = curr->data;
        copy->next = curr->next;
        copy->random = NULL;
        curr->next = copy;
        curr = copy->next;
    }

    // Step 2: Set random pointers for copies
    curr = head;
    while (curr) {
        if (curr->random)
            curr->next->random = curr->random->next;  // copy's random = original's random's copy
        curr = curr->next->next;
    }

    // Step 3: Separate original and copy lists
    RNode *cloneHead = head->next;
    curr = head;
    while (curr) {
        RNode *copy = curr->next;
        curr->next = copy->next;
        if (copy->next) copy->next = copy->next->next;
        curr = curr->next;
    }

    return cloneHead;
}
```

---

## PROBLEM 26: Sort Linked List Using Merge Sort

```c
Node* getMiddle(Node *head) {
    if (!head) return head;
    Node *slow = head, *fast = head->next;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    return slow;
}

Node* merge(Node *a, Node *b) {
    if (!a) return b;
    if (!b) return a;
    if (a->data <= b->data) { a->next = merge(a->next, b); return a; }
    else { b->next = merge(a, b->next); return b; }
}

Node* mergeSort(Node *head) {
    if (!head || !head->next) return head;

    Node *mid = getMiddle(head);
    Node *secondHalf = mid->next;
    mid->next = NULL;  // split

    Node *left = mergeSort(head);
    Node *right = mergeSort(secondHalf);

    return merge(left, right);
}

// Time: O(n log n), Space: O(log n) for recursion stack
// Best sorting algorithm for linked lists!
```

---

## PROBLEM 27: Delete ALL Occurrences of a Value

```c
Node* deleteAllOccurrences(Node *head, int key) {
    // Remove from beginning
    while (head && head->data == key) {
        Node *temp = head;
        head = head->next;
        free(temp);
    }

    if (!head) return NULL;

    // Remove from middle/end
    Node *curr = head;
    while (curr->next) {
        if (curr->next->data == key) {
            Node *temp = curr->next;
            curr->next = temp->next;
            free(temp);
        } else {
            curr = curr->next;
        }
    }

    return head;
}

// Input:  2->3->2->5->2->NULL, key=2
// Output: 3->5->NULL
```

---

## PROBLEM 28: Split a List into Two Halves

```c
void splitList(Node *head, Node **front, Node **back) {
    if (!head || !head->next) {
        *front = head;
        *back = NULL;
        return;
    }

    Node *slow = head, *fast = head->next;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }

    *front = head;
    *back = slow->next;
    slow->next = NULL;  // cut!
}

// Input:  1->2->3->4->5
// front = 1->2->3, back = 4->5
```

---

## PROBLEM 29: Pairwise Swap Elements

```c
void pairwiseSwap(Node *head) {
    Node *curr = head;
    while (curr && curr->next) {
        // Swap data of curr and curr->next
        int temp = curr->data;
        curr->data = curr->next->data;
        curr->next->data = temp;

        curr = curr->next->next;  // skip 2
    }
}

// Input:  1->2->3->4->5->NULL
// Output: 2->1->4->3->5->NULL
```

---

## PROBLEM 30: Complete Menu-Driven Program (All Operations)

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node { int data; struct Node *next; } Node;

Node* createNode(int d) {
    Node *n = (Node*)malloc(sizeof(Node));
    n->data = d; n->next = NULL; return n;
}

void display(Node *h) {
    if (!h) { printf("Empty list!\n"); return; }
    while (h) { printf("%d -> ", h->data); h = h->next; }
    printf("NULL\n");
}

Node* insertBeg(Node *h, int d) { Node *n=createNode(d); n->next=h; return n; }

Node* insertEnd(Node *h, int d) {
    Node *n=createNode(d);
    if(!h) return n;
    Node *t=h; while(t->next) t=t->next; t->next=n; return h;
}

Node* deleteBeg(Node *h) {
    if(!h){printf("Empty!\n");return NULL;}
    Node *t=h; h=h->next; free(t); return h;
}

Node* deleteEnd(Node *h) {
    if(!h){printf("Empty!\n");return NULL;}
    if(!h->next){free(h);return NULL;}
    Node *t=h; while(t->next->next) t=t->next;
    free(t->next); t->next=NULL; return h;
}

Node* reverse(Node *h) {
    Node *p=NULL,*c=h,*n;
    while(c){n=c->next;c->next=p;p=c;c=n;}
    return p;
}

int search(Node *h, int key) {
    int pos=1;
    while(h){if(h->data==key) return pos; h=h->next; pos++;}
    return -1;
}

int count(Node *h) { int c=0; while(h){c++;h=h->next;} return c; }

int main() {
    Node *head = NULL;
    int choice, val;

    while (1) {
        printf("\n========== LINKED LIST MENU ==========\n");
        printf("1. Insert at Beginning\n");
        printf("2. Insert at End\n");
        printf("3. Delete from Beginning\n");
        printf("4. Delete from End\n");
        printf("5. Display\n");
        printf("6. Reverse\n");
        printf("7. Search\n");
        printf("8. Count Nodes\n");
        printf("9. Exit\n");
        printf("Choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: printf("Value: "); scanf("%d", &val);
                    head = insertBeg(head, val); break;
            case 2: printf("Value: "); scanf("%d", &val);
                    head = insertEnd(head, val); break;
            case 3: head = deleteBeg(head); break;
            case 4: head = deleteEnd(head); break;
            case 5: display(head); break;
            case 6: head = reverse(head); printf("Reversed!\n"); break;
            case 7: printf("Search value: "); scanf("%d", &val);
                    { int p = search(head, val);
                      if(p!=-1) printf("Found at position %d\n", p);
                      else printf("Not found!\n"); }
                    break;
            case 8: printf("Count: %d\n", count(head)); break;
            case 9: printf("Goodbye!\n"); return 0;
            default: printf("Invalid choice!\n");
        }
    }
}
```

---

> **🎯 END OF ALL NOTES! You now have the most comprehensive Linked List notes covering:**
> - ✅ Foundations & Basics (Part 1)
> - ✅ All SLL Operations (Part 2)
> - ✅ Advanced SLL Operations (Part 3)
> - ✅ Doubly Linked Lists (Part 4)
> - ✅ Circular Linked Lists (Part 5)
> - ✅ Complexity & Applications (Part 6)
> - ✅ 82 VIVA Questions (Part 7)
> - ✅ 30 Coding Problems with Solutions (Part 8A + 8B)
