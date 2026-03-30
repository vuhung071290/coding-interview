# Data Structures & Algorithms

> Kiến thức DSA cần thiết cho Backend Engineers và phỏng vấn coding

## 🎯 Tại sao DSA quan trọng

**Cho Backend Engineers:**
• **Coding interviews** - 90% công ty tech kiểm tra tư duy algorithm
• **System design foundation** - Graph algorithms cho service topology, tree cho hierarchies
• **Performance optimization** - Chọn đúng data structure (O(1) vs O(log n) vs O(n))
• **Real-world problems** - LRU caches, rate limiters, schedulers, load balancers
• **Problem-solving** - Phân tích hệ thống phức tạp thành components algorithm

**Focus:** Ít competitive programming, nhiều patterns thực tế trong production systems.

---

# 1. 📊 Time Complexity & Problem-Solving

## Big O Reference
• **O(1)** - HashMap lookup, array access → Database indexes, caching
• **O(log n)** - Binary search, heap ops → Database queries, load balancing
• **O(n)** - Linear scan → Stream processing, batch jobs
• **O(n log n)** - Merge sort → Data processing, merge operations

## Problem-Solving Framework
1. **Hiểu** → Clarify requirements, edge cases
2. **Brute force** → Working solution trước
3. **Optimize** → Dùng data structures/algorithms phù hợp
4. **Test** → Verify với examples và edge cases

---

# 2. 🏗️ Data Structures Cần Thiết

## Essential cho Backend
• **HashMap** - O(1) avg → Caching, sessions, rate limiting
• **LRU Cache** - O(1) get/put → Database buffers, CDN ⭐ **Most common!**
• **Binary Tree** - O(log n) → DB indexes, hierarchies
• **Trie** - O(m) → Autocomplete, routing
• **Heap** - O(log n) → Schedulers, top-K, priority queues
• **Graph** - Varies → Dependencies, networks

## Graph Algorithms cho Backend
• **DFS/BFS** - Component analysis, shortest paths
• **Topological Sort** - Dependency resolution (build systems)
• **Union-Find** - Network connectivity, clustering
• **Dijkstra** - Network routing, shortest paths

---

# 3. 🎯 Algorithm Patterns & Recognition

## Essential Patterns
• **Two Pointers** - Sorted arrays, pairs → Database joins, duplicates
• **Sliding Window** - Subarray/substring → Rate limiting, streaming
• **Binary Search** - Sorted data, answer space → DB indexes, consensus
• **Dynamic Programming** - Optimization problems → Resource allocation, caching
• **Greedy** - Local → global optimal → Scheduling, MST

## Pattern Recognition Framework
• **Array/String + sorted** → Two pointers, binary search
• **Array/String + subarray** → Sliding window
• **Tree problems** → DFS/BFS recursion
• **Graph problems** → DFS/BFS, Union-Find
• **Optimization problems** → DP, greedy
• **System design** → Hash, heap, cache patterns
• **Keywords “min/max/k”** → Heap
• **Keywords “number of ways”** → DP
• **Keywords “prefix/interval/query”** → Segment Tree/BIT

---

# 4. 🏗️ Backend-Specific DSA

## Core Backend Algorithms
• **LRU Cache** - Redis, DB buffers 🔥 **Most Asked**
  - HashMap + Doubly Linked List = O(1) GET/PUT
• **Rate Limiter** - API throttling 🔥 **Very Common**
  - Fixed Window (simple), Sliding Window (accurate), Token Bucket (smooth)
• **Top-K Systems** - Real-time analytics ⭐ Common
  - Min-heap size K, Quickselect, Streaming median với 2 heaps
• **Consistent Hashing** - Load balancing ⭐ Common
• **Autocomplete System** - Trie + Heap hoặc Trie + HashMap

## Cache & Task Systems
• **LFU Cache** - Map + Frequency Map + Doubly List
• **Scheduler/Task Queue** - Priority Queue, Round-robin, Min-heap execution time
• **Load Balancing** - Round robin → Least connections → Consistent hashing

---

# 5. 🎯 Interview Success Framework

## Problem-Solving Steps (Ghi nhớ!)
1. **🤔 Clarify** - Requirements, edge cases, constraints
2. **💪 Brute Force** - Working solution trước
3. **⚡ Optimize** - Better complexity
4. **✨ Clean Code** - Readable, tested
5. **📊 Analyze** - Time/space complexity

## Interview Tactics

### Communication
• Giải thích tư duy trước khi code
• Viết skeleton trước → thêm logic dần
• Thinking aloud

### Edge Cases Checklist
• Empty input, One element
• Sorted/Reverse sorted, Duplicates
• Negative values, Large values
• Graph disconnected/cycles, Tree null children

### Khi bị stuck
• Quay về brute force, Pattern matching
• Convert sang dạng đã gặp, Run small examples
• Nhờ interviewer xác nhận assumptions

---

# 6. 📚 Lộ Trình Học & Resources

## Lộ Trình Tinh Gọn

### Giai đoạn 1 - Nền tảng (7-10 ngày)
• Complexity Analysis, Array/String basics
• HashMap/Set operations, Stack/Queue fundamentals
• Sliding Window technique, Two Pointers pattern

### Giai đoạn 2 - Cốt lõi (10-20 ngày)
• Trees (Binary Tree, BST), Graphs (DFS/BFS)
• Heap operations, Union-Find
• Sorting/Searching algorithms, Monotonic Stack

### Giai đoạn 3 - Advanced (10-20 ngày)
• Dynamic Programming classics
• DP subsequences và patterns, Binary Search on Answer Space
• Advanced graph algorithms

### Giai đoạn 4 - Backend Special (7 ngày)
• LRU/LFU Cache implementation, Rate limiter designs
• Top-K problems, Scheduler problems

### Giai đoạn 5 - Practice Sets
• Blind 75 fundamentals, NeetCode 150 comprehensive
• Striver SDE backend-focused, Company-specific problems

## Resources

### Books
• **[Grokking Algorithms](pdf/Grokking-Algorithms.pdf)** - Visual approach, great for beginners
• **[The Algorithm Design Manual](pdf/The-Algorithm-Design-Manual-3rd-Edition.pdf)** - Practical algorithm design
• **[Introduction to Algorithms](pdf/Introduction-To-Algorithms-3rd-Edition.pdf)** - Comprehensive reference

### Practice Sets Bắt Buộc
• [Blind 75](https://www.techinterviewhandbook.org/best-practice-questions/) - Core fundamentals
• [NeetCode 150](https://neetcode.io/practice?tab=neetcode150) - Comprehensive coverage
• [Striver SDE](https://takeuforward.org/interviews/strivers-sde-sheet-top-coding-interview-problems/) - Interview-focused

### Online Platforms
• **LeetCode** - Most comprehensive, company-specific problems
• **NeetCode** - Curated problems với video explanations
• **HackerRank** - Interview practice và contests
• **Pramp** - Mock interviews với peers

## Complexity Cheat Sheet

### Time Complexities Cần Nhớ
• HashMap: O(1) avg, O(n) worst • Binary search: O(log n)
• Heap ops: O(log n) • Sorting: O(n log n) • DFS/BFS: O(V + E)

### Space Complexities
• Recursion depth: O(height) trees • Graph algorithms: O(V) visited
• DP problems: Often O(n) hoặc O(n²)

### Backend Interview Favorites
• LRU Cache implementation • Design rate limiter • Top-K streaming data
• Graph traversal service dependencies • Binary search variations