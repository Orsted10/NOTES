# 📘 PART 6: TIME/SPACE COMPLEXITY & APPLICATIONS

---

## 6.1 — Time Complexity of ALL Operations

### Singly Linked List (SLL)

| Operation | Time Complexity | Why? |
|-----------|:-:|------|
| **Insert at Beginning** | O(1) | Just update head pointer and one link |
| **Insert at End** | O(n) | Must traverse entire list to find last node |
| **Insert at Position k** | O(k) | Traverse to position k |
| **Delete from Beginning** | O(1) | Just update head pointer |
| **Delete from End** | O(n) | Must find second-to-last node |
| **Delete at Position k** | O(k) | Traverse to position k |
| **Delete by Value** | O(n) | Must search for the value first |
| **Search** | O(n) | May need to check every node |
| **Access kth element** | O(k) | Must traverse from head |
| **Count nodes** | O(n) | Visit every node |
| **Reverse** | O(n) | Visit every node once |
| **Find Middle** | O(n) | Slow-fast pointer traverses half each |

### Doubly Linked List (DLL)

| Operation | Time Complexity | Advantage over SLL? |
|-----------|:-:|------|
| **Insert at Beginning** | O(1) | Same |
| **Insert at End** | O(n)* | Same (*O(1) if tail pointer maintained) |
| **Delete from Beginning** | O(1) | Same |
| **Delete from End** | O(n)* | Same (*O(1) if tail pointer maintained) |
| **Delete given node pointer** | **O(1)** | ✅ SLL needs O(n) to find previous! |
| **Traverse Forward** | O(n) | Same |
| **Traverse Backward** | O(n) | ✅ SLL can't do this! |

### Circular Linked List

| Operation | Time Complexity | Note |
|-----------|:-:|------|
| **Insert at Beginning (CSLL)** | O(n) | Must find last node to update its next |
| **Insert at Beginning (CDLL)** | **O(1)** | ✅ head->prev gives last node directly! |
| **Insert at End (CSLL)** | O(n) | Must traverse to last |
| **Insert at End (CDLL)** | **O(1)** | ✅ head->prev gives last node directly! |

### Arrays vs Linked Lists — Complexity Comparison

| Operation | Array | Linked List |
|-----------|:-----:|:-----------:|
| Access by index | **O(1)** ✅ | O(n) ❌ |
| Search | O(n) | O(n) |
| Insert at beginning | O(n) ❌ | **O(1)** ✅ |
| Insert at end | **O(1)** amortized ✅ | O(n)* |
| Insert at middle | O(n) | O(n) for finding + O(1) for inserting |
| Delete at beginning | O(n) ❌ | **O(1)** ✅ |
| Delete at end | **O(1)** ✅ | O(n)* |
| Memory per element | Just data | Data + pointer(s) ❌ |
| Memory allocation | Contiguous block | Scattered (flexible) ✅ |

*Can be made O(1) with a tail pointer.

---

## 6.2 — Space Complexity

| Structure | Space per Node | Total for n nodes |
|-----------|:-:|:-:|
| Array of ints | 4 bytes | 4n bytes |
| SLL of ints | 4 + 8 = 12 bytes (data + 1 pointer) | 12n bytes |
| DLL of ints | 4 + 8 + 8 = 20 bytes (data + 2 pointers) | 20n bytes |

(Assuming 4-byte int, 8-byte pointer on 64-bit system)

**Linked lists use 2x to 5x more memory** than arrays for the same data!

---

## 6.3 — Advantages of Linked Lists

1. **Dynamic Size** — No need to declare size upfront; grows/shrinks at runtime
2. **Efficient Insertion/Deletion** — O(1) at known positions (no shifting)
3. **No Memory Waste** — Uses exactly as much memory as needed
4. **No Contiguous Memory Required** — Works even with fragmented RAM
5. **Easy to Implement Stacks, Queues** — Natural fit for these data structures

## 6.4 — Disadvantages of Linked Lists

1. **No Random Access** — Can't do `list[5]`; must traverse from head
2. **Extra Memory** — Each node needs extra space for pointer(s)
3. **Poor Cache Performance** — Nodes scattered in memory → cache misses
4. **Reverse Traversal Difficult** — SLL can only go forward
5. **Not Suitable for Binary Search** — O(n) access kills the O(log n) advantage

---

## 6.5 — Real-World Applications

| Application | Which Type | Why |
|-------------|-----------|-----|
| **Undo/Redo** (text editors) | DLL | Go back (undo) and forward (redo) |
| **Browser History** | DLL | Back and forward buttons |
| **Music Player Playlist** | Circular SLL/DLL | Loop back to first song |
| **OS Process Scheduling** | Circular LL | Round-robin: cycle through processes |
| **Hash Table Chaining** | SLL | Each bucket is a linked list of collisions |
| **Polynomial Representation** | SLL | Each term is a node: coefficient + exponent |
| **Stack Implementation** | SLL | Push/Pop = Insert/Delete at beginning |
| **Queue Implementation** | SLL with tail | Enqueue at tail, Dequeue from head |
| **Graph Adjacency List** | SLL | Each vertex stores list of neighbors |
| **Memory Management** | DLL | OS free-list tracks available memory blocks |
| **Image Viewer** | Circular DLL | Next/Previous image, wraps around |

---

## 6.6 — When to Use What?

```
Need random access (arr[i])?          → Use ARRAY
Need frequent insert/delete?          → Use LINKED LIST
Only forward traversal?               → Use SINGLY LINKED LIST
Need backward traversal too?          → Use DOUBLY LINKED LIST
Need to cycle back to start?          → Use CIRCULAR LINKED LIST
Need both directions + cycling?       → Use CIRCULAR DOUBLY LINKED LIST
Fixed size, known at compile time?    → Use ARRAY
Size changes frequently at runtime?   → Use LINKED LIST
```

---

> **🎯 End of Part 6: You now understand all complexity analysis, tradeoffs, advantages, disadvantages, and real-world applications. Next: VIVA Questions!**
