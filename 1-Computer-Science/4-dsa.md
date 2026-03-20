# DSA for SWE/Backend

---

# 1. Foundations & Core Concepts

## 1.1. Complexity
- Time & Space Complexity
- Big O, Ω, Θ
- Amortized Analysis (ArrayList, HashMap, Union-Find)

## 1.2. Recursion & Backtracking
- Base/recursive case
- Memoization (Top-down), Tabulation (Bottom-up)
- Ứng dụng: DP, tree traversal, combinations, permutations, subsets

## 1.3. Bit Manipulation
- AND, OR, XOR, SHIFT
- Common tricks: check bit, count bits, mask subsets
- Ứng dụng: DP on bitmask, optimization, set operations

---

# 2. Data Structures

## 2.1. Linear Structures
- Array, String: sliding window, prefix sum, two pointers
- Linked List: fast-slow pointer, cycle detection, reverse list
- Stack/Queue: monotonic stack, queue cho BFS
- Deque: sliding window max/min O(n)

## 2.2. Non-linear Structures
- Trees: Binary Tree, BST
    - Traversals: preorder, inorder, postorder, level-order
- Trie: prefix lookup, word search
- Graph: adjacency list, DFS, BFS, topo sort
- Union-Find (DSU): cycle detection, connected components

## 2.3. Hashing
- HashMap, HashSet
- Collision handling (biết khái niệm là đủ)
- LRU cache (Map + DLL)

## 2.4. Heap / Priority Queue
- Min/Max Heap
- Ứng dụng: Dijkstra, top-k, median queue

## 2.5. Advanced Structures (chỉ cần biết, không cần code)
- Segment Tree: range query/update
- Fenwick Tree (BIT): prefix sums nâng cao
- Bloom Filter: membership test

---

# 3. Algorithms

## 3.1. Sorting & Searching
- Merge Sort, Quick Sort
- Binary Search (index / answer)
- Partitioning logic (quicksort / quickselect)

## 3.2. Greedy
- Interval scheduling
- Merge intervals
- Huffman coding (hiểu concept)

## 3.3. Divide & Conquer
- Merge Sort
- Quick Sort
- Closest pair (hiểu concept)

## 3.4. Dynamic Programming (DP)
- Knapsack
- Longest Increasing Subsequence (LIS)
- DP on grids
- DP state design: dp[i], dp[i][j], dp[mask], dp[index][state]
- DP on trees (biết dạng tổng quát)

## 3.5. Graph Algorithms
- Shortest Path: Dijkstra
- MST: Kruskal (DSU), Prim
- Topological Sort
- Cycle detection: DFS / DSU
- SCC (Tarjan/Kosaraju – chỉ cần biết khái niệm)

---

# 4. Patterns & Techniques

- Sliding Window
- Two Pointers
- Fast & Slow Pointers
- Prefix Sum / Difference Array
- Monotonic Stack / Queue
- Binary Search on Answer
- Backtracking Templates
- DP Templates (1D/2D/Bitmask)
- Union-Find patterns
- Graph traversal patterns (DFS/BFS)

---

# 5. DSA Thực Chiến Cho Backend Interviews

## 5.1. LRU Cache
- HashMap + Doubly Linked List
- O(1) GET/PUT
- Liên quan: caching layer, rate-limiter, session store

## 5.2. LFU Cache
- Map + Frequency Map + Doubly List
- Update frequency O(1)

## 5.3. Rate Limiter
- Fixed Window Counter
- Sliding Window Log
- Token Bucket
- Leaky Bucket

## 5.4. Autocomplete System
- Trie + Heap / Trie + HashMap

## 5.5. Scheduler / Task Queue
- Priority Queue
- Round-robin (Queue)
- Min-heap for execution time

## 5.6. Top-k Systems
- Heap
- Quickselect
- Streaming median: 2 heaps

---

# 6. Coding Interview Tactics

## 6.1. Cách phân tích đề hiệu quả
- Xác định input size
- Khung phân loại nhanh:
    - Array/String → Sliding window / two pointers
    - Tree → DFS/BFS
    - Graph → BFS/DFS/Topo
    - “min/max/k” → Heap
    - “number of ways” → DP
    - “prefix/interval/query” → Segment Tree/BIT

## 6.2. Cách nói chuyện với interviewer
- Giải thích tư duy trước khi code
- Viết skeleton trước → thêm logic dần
- Thinking aloud

## 6.3. Edge Case Checklist
- Empty input
- One element
- Sorted / Reverse sorted
- Duplicates
- Negative values
- Large values
- Graph disconnected / cycles
- Tree null children

## 6.4. Clean Code Checklist
- Tên biến rõ ràng
- Tách function nhỏ
- Tránh nested logic sâu
- Comment logic quan trọng
- Phân tích complexity sau khi code

## 6.5. Khi bị stuck
- Quay về brute force
- Pattern matching
- Convert dạng đã gặp
- Run small examples
- Nhờ interviewer xác nhận assumptions

---

# 7. Practice Sets

## Bắt buộc
- [Blind 75](https://www.techinterviewhandbook.org/best-practice-questions/)
- [NeetCode 150](https://neetcode.io/practice?tab=neetcode150)
- [Striver SDE](https://takeuforward.org/interviews/strivers-sde-sheet-top-coding-interview-problems/)

## Tùy chọn
- [Top Interview 150](https://leetcode.com/studyplan/top-interview-150/)

---

# 8. Lộ Trình Học Tinh Gọn

## Giai đoạn 1 – Nền tảng (7–10 ngày)
- Complexity
- Array/String
- HashMap/Set
- Stack/Queue
- Sliding Window
- Two Pointers

## Giai đoạn 2 – Cốt lõi (10–20 ngày)
- Trees
- Graphs (DFS/BFS)
- Heap
- Union-Find
- Sorting/Searching
- Monotonic Stack

## Giai đoạn 3 – DP & Patterns (10–20 ngày)
- Classic DP
- DP subsequences
- DP patterns
- Binary Search on Answer

## Giai đoạn 4 – Backend-special (7 ngày)
- LRU/LFU
- Rate limiter
- Top-k
- Scheduler problems

## Giai đoạn 5 – Luyện đề
- Blind 75
- NeetCode 150
- Striver SDE
- Top Interview 150

# 9. Reference Materials

## Books
- [Grokking Algorithms](./pdf/Grokking-Algorithms.pdf)
- [Introduction to Algorithms](./pdf/Introduction-To-Algorithms-3rd-Edition.pdf)
- [The Algorithm Design Manual](./pdf/The-Algorithm-Design-Manual-3rd-Edition.pdf)
- [Algorithms](./pdf/Algorithms-4th-Edition.pdf)
- [Algorithm Design](./pdf/Algorithm-Design.pdf)
- [Data Structures and Algorithms Made Easy in Java](./pdf/Data-Structures-and-Algorithms-Made-Easy-in-Java.pdf)
- [Data Structures and Algorithms in Java](./pdf/Data-Structures-and-Algorithms-in-Java.pdf)
- [Complete Data structures and Algorithms Guide](./pdf/Complete-Data-structures-and-Algorithms-Guide.pdf)
