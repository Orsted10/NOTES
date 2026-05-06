# 📘 PART 7: VIVA / THEORY QUESTIONS WITH ANSWERS (80+ Questions)

---

## BASIC CONCEPTS (Q1–Q20)

**Q1. What is a Linked List?**
> A linked list is a linear data structure where elements (nodes) are stored in non-contiguous memory locations. Each node contains a data field and a pointer to the next node in the sequence.

**Q2. What is a Node?**
> A node is the basic building block of a linked list. It contains two parts: (1) Data — the actual value, and (2) Next — a pointer/reference to the next node.

**Q3. What is a self-referential structure?**
> A structure that contains a pointer to a variable of its own type. The Node struct is self-referential because it has a `struct Node *next` pointer inside itself.

**Q4. What is the difference between an Array and a Linked List?**
> Arrays store elements in contiguous memory with fixed size and O(1) random access. Linked Lists store elements in scattered memory with dynamic size, O(n) access, but O(1) insertion/deletion at known positions.

**Q5. What are the types of Linked Lists?**
> (1) Singly Linked List, (2) Doubly Linked List, (3) Circular Singly Linked List, (4) Circular Doubly Linked List.

**Q6. What is the Head pointer?**
> The head is a pointer that stores the address of the first node. It's the only entry point to the linked list. If head = NULL, the list is empty.

**Q7. What does NULL signify in a linked list?**
> NULL marks the end of the list. The last node's `next` pointer is NULL, indicating there are no more nodes.

**Q8. Why do we use `malloc()` for linked lists?**
> Because linked list nodes are created at runtime (dynamically). `malloc()` allocates memory from the heap during program execution, unlike arrays which are allocated at compile time.

**Q9. What is `sizeof()` and why is it used with malloc?**
> `sizeof()` returns the number of bytes a data type occupies. We use `sizeof(struct Node)` to tell malloc exactly how much memory to allocate for one node.

**Q10. What happens if we lose the head pointer?**
> We lose access to the ENTIRE linked list. All nodes become unreachable and their memory cannot be freed, causing a memory leak.

**Q11. What is a memory leak?**
> A memory leak occurs when dynamically allocated memory is not freed and its address is lost. The program can no longer access or release that memory. Over time, this can exhaust available memory.

**Q12. Why can't we do `struct Node next;` (without pointer)?**
> It would create an infinite-sized structure (Node containing Node containing Node...). A pointer has a fixed size (4 or 8 bytes), so the compiler can determine the struct's size.

**Q13. What is the arrow operator (->)?**
> `ptr->member` is a shorthand for `(*ptr).member`. It dereferences a pointer and accesses a member of the struct it points to.

**Q14. What is the difference between `struct Node *next` and `struct Node next`?**
> `struct Node *next` stores an ADDRESS (4/8 bytes, fixed). `struct Node next` would try to embed an ENTIRE Node inside another Node (impossible, infinite recursion).

**Q15. Can a linked list store different data types?**
> Not directly in C (each node has a fixed type). However, you can use `void *data` pointer to store any type, or use a union within the node.

**Q16. What is the time complexity to access the kth element in a linked list?**
> O(k) — you must traverse from the head through k nodes sequentially. Unlike arrays which give O(1) access.

**Q17. Can we do binary search on a linked list?**
> Technically possible but impractical. Binary search requires O(1) random access (like arrays). In a linked list, accessing the middle element takes O(n), making the overall complexity O(n log n) instead of O(log n).

**Q18. What is the minimum information needed to represent an entire linked list?**
> Just the head pointer. From the head, you can traverse to every other node by following the `next` pointers.

**Q19. What is an empty linked list?**
> A linked list where `head == NULL`. It contains no nodes.

**Q20. What is `free()` and why is it important?**
> `free()` returns dynamically allocated memory back to the operating system. Without it, the memory stays allocated even when no longer needed, causing memory leaks.

---

## OPERATIONS & ALGORITHMS (Q21–Q40)

**Q21. How do you traverse a linked list?**
> Start from head, visit each node, and follow the `next` pointer until you reach NULL. Use a `while (current != NULL)` loop.

**Q22. Why do we use `current->next != NULL` instead of `current != NULL` when finding the last node?**
> Using `current != NULL` would move current past the last node (to NULL). We need to STOP at the last node to modify its `next` pointer, so we check `current->next != NULL`.

**Q23. How do you insert at the beginning of an SLL?**
> (1) Create new node, (2) Set new node's next to current head, (3) Update head to new node. Time: O(1).

**Q24. Why do we need to return the head or use pointer-to-pointer for insertion at the beginning?**
> Because C passes arguments by value. Modifying `head` inside a function only changes the local copy. Returning the new head or using `Node **head` modifies the original pointer.

**Q25. How do you delete a node without knowing its previous node in SLL?**
> Copy the data from the NEXT node into the current node, then delete the NEXT node. This effectively "replaces" the current node. Note: This doesn't work for the LAST node.

**Q26. How do you find the middle element in one pass?**
> Use the slow-fast pointer technique (tortoise and hare). Slow moves 1 step, fast moves 2 steps. When fast reaches the end, slow is at the middle.

**Q27. How does Floyd's Cycle Detection work?**
> Two pointers (slow and fast) traverse the list. Slow moves 1 step, fast moves 2 steps. If there's a cycle, they will eventually meet. If fast reaches NULL, there's no cycle.

**Q28. How do you reverse a linked list iteratively?**
> Use three pointers: prev (starts at NULL), current (starts at head), nextNode. In each step: save nextNode, reverse current's link to prev, advance prev and current.

**Q29. What's the time complexity of reversing a linked list?**
> O(n) — we visit each node exactly once. Space: O(1) for iterative, O(n) for recursive (call stack).

**Q30. How do you merge two sorted linked lists?**
> Compare heads of both lists, pick the smaller one, and recursively/iteratively merge the rest. Similar to the merge step in merge sort.

**Q31. How do you detect if two linked lists intersect?**
> Find lengths of both lists, advance the longer list by the difference, then traverse both simultaneously. When pointers meet, that's the intersection.

**Q32. How to find the nth node from the end?**
> Two-pointer approach: advance first pointer n steps, then move both first and second together. When first reaches NULL, second is at nth from end.

**Q33. How do you check if a linked list is a palindrome?**
> Find middle, reverse the second half, compare both halves node by node, optionally restore the list.

**Q34. Can you sort a linked list? Which algorithm is best?**
> Yes. Merge Sort is best for linked lists — O(n log n) time, no random access needed. Bubble Sort works but is O(n²). Quick Sort is possible but less efficient.

**Q35. How do you remove duplicates from a sorted list?**
> Traverse the list; if current node's data equals next node's data, delete the next node. Don't advance current (more duplicates might follow).

**Q36. How do you remove duplicates from an unsorted list?**
> Method 1: For each node, remove all future occurrences — O(n²). Method 2: Use a hash set to track seen values — O(n) time, O(n) space.

**Q37. How to find the length of a loop in a linked list?**
> First detect the cycle using Floyd's. Once slow and fast meet, keep one pointer fixed and move the other, counting steps until they meet again.

**Q38. How to add two numbers represented as linked lists?**
> Traverse both lists simultaneously, add corresponding digits + carry. Create a new list with result digits. Handle different lengths and final carry.

**Q39. What is the difference between `head = head->next` and `temp = head; head = head->next; free(temp);`?**
> The first just moves head forward but LOSES the old node (memory leak). The second saves the old node, moves head, then properly frees the old node.

**Q40. How do you create a linked list from an array?**
> Iterate through the array, create a node for each element, and add it to the linked list using insert at end.

---

## DOUBLY & CIRCULAR LINKED LISTS (Q41–Q55)

**Q41. What is a Doubly Linked List?**
> A linked list where each node has THREE fields: prev pointer, data, and next pointer. It allows traversal in both forward and backward directions.

**Q42. Advantage of DLL over SLL?**
> (1) Bidirectional traversal, (2) Deletion of a given node is O(1) since we have the prev pointer, (3) Easier insertion before a given node.

**Q43. Disadvantage of DLL compared to SLL?**
> (1) Extra memory per node (prev pointer), (2) More complex pointer manipulation, (3) More pointers to update during operations.

**Q44. How do you reverse a DLL?**
> Simply swap the `prev` and `next` pointers of every node. Much simpler than SLL reversal.

**Q45. What is a Circular Linked List?**
> A linked list where the last node points back to the first node instead of NULL. There is no NULL in the list.

**Q46. How does traversal differ in a Circular Linked List?**
> Use a `do-while` loop instead of `while`. Check if `current` has come back to `head` to stop. A `while` loop won't start because `current == head` initially.

**Q47. Why is Circular DLL efficient for insertion at both ends?**
> `head->prev` directly gives the last node! No traversal needed. Both insert at beginning and end are O(1).

**Q48. How do you check if a linked list is circular?**
> Traverse the list. If you reach NULL, it's not circular. If you come back to the head, it is circular. Or use Floyd's algorithm.

**Q49. What is the difference between a Circular LL and a LL with a cycle?**
> In a Circular LL, the last node intentionally points to the first — it's a design choice. A cycle is a BUG where some node incorrectly points to an earlier node, creating an unintended loop.

**Q50. In a CDLL, how do you access the last node in O(1)?**
> Simply use `head->prev`. The head's previous pointer always points to the last node.

**Q51. Give a real-life example of each type.**
> SLL: Singly-direction train coaches. DLL: Browser history (back/forward). CSLL: Round-robin game turns. CDLL: Music player with prev/next and repeat-all.

**Q52. Can you implement a stack using a linked list?**
> Yes. Push = Insert at beginning. Pop = Delete from beginning. Both O(1). The head of the list is the top of the stack.

**Q53. Can you implement a queue using a linked list?**
> Yes. Enqueue = Insert at end. Dequeue = Delete from beginning. Using a tail pointer makes both O(1).

**Q54. What is a Header Linked List?**
> A linked list that has a special "header" or "dummy" node at the beginning. This node doesn't store meaningful data but simplifies operations (no special cases for empty list or head changes).

**Q55. What is a Sentinel Node?**
> Same as header/dummy node — a special node at the beginning (or end) that acts as a boundary marker to simplify edge case handling.

---

## ADVANCED / TRICKY QUESTIONS (Q56–Q70)

**Q56. What happens if you don't free a linked list?**
> Memory leak. All nodes remain allocated in heap memory even after the program section that uses them is done. For long-running programs, this can eventually exhaust memory.

**Q57. What is a dangling pointer?**
> A pointer that points to memory that has been freed. Accessing it causes undefined behavior (crash, garbage data, etc.). After `free(ptr)`, always set `ptr = NULL`.

**Q58. Difference between `free(head)` and freeing the entire list?**
> `free(head)` only frees the FIRST node. The remaining nodes are still allocated but now unreachable (memory leak). You must traverse and free each node individually.

**Q59. What is the advantage of using `typedef` with struct?**
> Avoids writing `struct Node` everywhere. With `typedef struct Node {...} Node;`, you can simply write `Node *ptr;` instead of `struct Node *ptr;`.

**Q60. Can a linked list have duplicate values?**
> Yes, unless you specifically enforce uniqueness during insertion.

**Q61. How to find the intersection of two linked lists?**
> Use the length-difference method or the two-pointer method where both pointers eventually traverse both lists and meet at the intersection.

**Q62. Difference between `head->next = NULL` and `head = NULL`?**
> `head->next = NULL` makes the first node's next pointer NULL (disconnects the rest). `head = NULL` makes head point to nothing (you lose the list, but nodes still exist in memory).

**Q63. What is a Sparse Matrix and how are linked lists used?**
> A matrix with mostly zero values. Instead of storing all elements, store only non-zero elements as nodes (row, column, value) in a linked list, saving memory.

**Q64. What is a Polynomial representation using linked lists?**
> Each term of the polynomial is a node with coefficient and exponent. For example, 3x² + 5x + 2 becomes: [3,2] → [5,1] → [2,0] → NULL.

**Q65. How to implement LRU Cache using linked lists?**
> Use a DLL + hash map. Most recently used items at head. When cache is full, remove from tail (least recently used). Hash map provides O(1) lookup.

**Q66. Can two linked lists share nodes?**
> Yes, this is what happens in intersecting linked lists. After the intersection point, both lists share the same nodes.

**Q67. What is the XOR linked list?**
> A memory-efficient DLL where each node stores XOR of prev and next addresses instead of two separate pointers. Uses only one pointer field but allows bidirectional traversal.

**Q68. What is a Skip List?**
> A linked list with multiple layers of "express lanes." Each higher layer skips more nodes, allowing O(log n) search — like a linked list version of binary search.

**Q69. How would you implement a linked list without using structures?**
> Use two parallel arrays: one for data, one for "next" indices. Array index acts as the "address." This is called an array-based linked list or implicit linked list.

**Q70. What is memory fragmentation and how do linked lists contribute?**
> Memory fragmentation is when free memory is broken into small non-contiguous chunks. Since linked lists use many small malloc calls, they scatter nodes across memory, potentially worsening fragmentation.

---

## COMPARISON & APPLICATION QUESTIONS (Q71–Q80+)

**Q71. When would you choose a linked list over an array?**
> When you need frequent insertions/deletions, unknown size at compile time, or don't need random access.

**Q72. When would you choose an array over a linked list?**
> When you need random access, cache-friendly performance, known fixed size, or minimal memory overhead.

**Q73. Which data structures use linked lists internally?**
> Stacks, Queues, Hash Tables (chaining), Graphs (adjacency lists), Symbol Tables, OS memory management (free lists).

**Q74. What is the space overhead ratio for SLL vs array?**
> For storing integers: SLL uses 12 bytes/node (4 data + 8 pointer) vs array's 4 bytes/element. That's 3x overhead or 200% extra memory.

**Q75. How does cache locality affect linked list vs array performance?**
> Arrays are stored contiguously, so accessing sequential elements utilizes CPU cache efficiently. Linked list nodes are scattered, causing frequent cache misses, which makes traversal slower in practice even with same O(n) complexity.

**Q76. What is the Josephus Problem and how does it relate to circular linked lists?**
> N people stand in a circle. Every kth person is eliminated. Circular linked list naturally models this — traverse k nodes, delete, repeat until one remains.

**Q77. How to implement a doubly linked list using only one pointer per node?**
> XOR Linked List: Store `prev XOR next` in one field. Given either adjacent node, XOR with stored value gives the other adjacent node.

**Q78. What is the difference between a linked list and a dynamic array (like ArrayList)?**
> Dynamic arrays resize (usually doubling) when full. They maintain contiguous memory and O(1) access but have O(n) worst-case insertion. Linked lists have O(1) insertion but O(n) access.

**Q79. Can you have a generic linked list in C?**
> Yes, using `void *data` pointer. Each node stores a void pointer to any type of data. You cast it to the appropriate type when accessing.

**Q80. What is a Multi-level Linked List (Flattening Problem)?**
> A linked list where each node may have a "child" pointer to another linked list. Flattening means merging all levels into a single-level list.

**Q81. What are the common mistakes when implementing linked lists?**
> (1) Not checking NULL, (2) Not freeing memory, (3) Losing head pointer, (4) Not updating prev in DLL, (5) Off-by-one errors in position-based operations, (6) Using freed pointers.

**Q82. What is a tail pointer and when should you use it?**
> A pointer to the LAST node of the list. Useful when you frequently insert at the end — makes it O(1) instead of O(n).

---

> **🎯 End of Part 7: 82 comprehensive VIVA questions covering every aspect. Next: Practical coding questions!**
