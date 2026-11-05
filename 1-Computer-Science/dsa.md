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

## 1. 📊 Time Complexity & Problem-Solving

### Big O Reference
• **O(1)** - HashMap lookup, array access → Database indexes, caching
• **O(log n)** - Binary search, heap ops → Database queries, load balancing
• **O(n)** - Linear scan → Stream processing, batch jobs
• **O(n log n)** - Merge sort → Data processing, merge operations
• **O(n²)** - Nested loops, brute force → Acceptable cho n nhỏ, cần optimize cho n lớn
• **O(2ⁿ)** - Exponential, subsets/backtracking → Chỉ OK khi n ≤ 20, thường cần DP

### Problem-Solving Framework
1. **Hiểu** → Clarify requirements, edge cases
2. **Brute force** → Working solution trước
3. **Optimize** → Dùng data structures/algorithms phù hợp
4. **Test** → Verify với examples và edge cases

---

## 2. 🏗️ Data Structures Cần Thiết

### Linear Structures (Nền tảng)
• **Array** - O(1) access, O(n) insert/delete → Base cho hầu hết thuật toán
• **LinkedList** - O(1) insert/delete, O(n) access → Building block cho LRU, queue
• **Stack** - LIFO, O(1) push/pop → Parser, DFS, undo, monotonic stack
• **Queue / Deque** - FIFO, O(1) enqueue/dequeue → BFS, task queue, sliding window

### Essential cho Backend
• **HashMap / HashSet** - O(1) avg → Caching, sessions, rate limiting, dedup
• **LRU Cache** - O(1) get/put → Database buffers, CDN ⭐ **Most common!**
• **Binary Tree / BST** - O(log n) → DB indexes, hierarchies
• **Trie** - O(m) → Autocomplete, routing, IP prefix matching
• **Heap (PriorityQueue)** - O(log n) → Schedulers, top-K, priority queues
• **Graph** - Varies → Dependencies, networks, social connections
• **Segment Tree / BIT (Fenwick)** - O(log n) → Range queries, prefix sums dynamic

### Graph Algorithms cho Backend
• **DFS/BFS** - Component analysis, shortest paths
• **Topological Sort** - Dependency resolution (build systems)
• **Union-Find** - Network connectivity, clustering
• **Dijkstra** - Network routing, shortest paths

---

## 3. 🎯 Algorithm Patterns & Recognition

### Essential Patterns
• **Two Pointers** - Sorted arrays, pairs → Database joins, duplicates
• **Fast & Slow Pointers** - Cycle detection → LinkedList cycle, middle node
• **Sliding Window** - Subarray/substring → Rate limiting, streaming
• **Prefix Sum / Difference Array** - Range sum queries → Analytics, cumulative stats
• **Binary Search** - Sorted data, answer space → DB indexes, consensus
• **Monotonic Stack/Queue** - Next greater/smaller → Stock span, sliding window max
• **Backtracking** - Permutations, combinations → N-Queens, subsets, word search
• **Dynamic Programming** - Optimization problems → Resource allocation, caching
• **Greedy** - Local → global optimal → Scheduling, MST

### Pattern Recognition Framework
• **Array/String + sorted** → Two pointers, binary search
• **Array/String + subarray** → Sliding window
• **Tree problems** → DFS/BFS recursion
• **Graph problems** → DFS/BFS, Union-Find
• **Optimization problems** → DP, greedy
• **System design** → Hash, heap, cache patterns
• **Keywords “min/max/k”** → Heap
• **Keywords “number of ways”** → DP
• **Keywords “prefix/interval/query”** → Prefix Sum / Segment Tree / BIT
• **Keywords “all combinations/permutations”** → Backtracking
• **Keywords “next greater/smaller”** → Monotonic Stack

---

## 4. 🏗️ Backend-Specific DSA

### Core Backend Algorithms
• **LRU Cache** - Redis, DB buffers 🔥 **Most Asked**
  - HashMap + Doubly Linked List = O(1) GET/PUT
• **Rate Limiter** - API throttling 🔥 **Very Common**
  - Fixed Window (simple), Sliding Window (accurate), Token Bucket (smooth)
• **Top-K Systems** - Real-time analytics ⭐ Common
  - Min-heap size K, Quickselect, Streaming median với 2 heaps
• **Consistent Hashing** - Load balancing ⭐ Common
• **Autocomplete System** - Trie + Heap hoặc Trie + HashMap

### Cache & Task Systems
• **LFU Cache** - Map + Frequency Map + Doubly List
• **Scheduler/Task Queue** - Priority Queue, Round-robin, Min-heap execution time
• **Load Balancing** - Round robin → Least connections → Consistent hashing

### Probabilistic & Distributed Structures
• **Bloom Filter** - Membership check với false positive → Cache pre-check, dedup ở scale lớn
• **Skip List** - O(log n) probabilistic → Redis Sorted Set (ZSET) implementation
• **Merkle Tree** - Hash tree → Git, blockchain, DynamoDB anti-entropy
• **HyperLogLog** - Cardinality estimation → Unique visitors, distinct counts

---

## 5. 🎯 Interview Success Framework

### Problem-Solving Steps (Ghi nhớ!)
1. **🤔 Clarify** - Requirements, edge cases, constraints
2. **💪 Brute Force** - Working solution trước
3. **⚡ Optimize** - Better complexity
4. **✨ Clean Code** - Readable, tested
5. **📊 Analyze** - Time/space complexity

### Interview Tactics

#### Communication
• Giải thích tư duy trước khi code
• Viết skeleton trước → thêm logic dần
• Thinking aloud

#### Edge Cases Checklist
• Empty input, One element
• Sorted/Reverse sorted, Duplicates
• Negative values, Large values
• Graph disconnected/cycles, Tree null children

#### Khi bị stuck
• Quay về brute force, Pattern matching
• Convert sang dạng đã gặp, Run small examples
• Nhờ interviewer xác nhận assumptions

---

## 6. 📚 Lộ Trình Học & Resources

### Lộ Trình Tinh Gọn

#### Giai đoạn 1 - Nền tảng (7-10 ngày)
• Complexity Analysis, Array/String basics
• HashMap/Set operations, Stack/Queue fundamentals
• Sliding Window technique, Two Pointers pattern

#### Giai đoạn 2 - Cốt lõi (10-20 ngày)
• Trees (Binary Tree, BST), Graphs (DFS/BFS)
• Heap operations, Union-Find
• Sorting/Searching algorithms, Monotonic Stack

#### Giai đoạn 3 - Advanced (10-20 ngày)
• Dynamic Programming classics
• DP subsequences và patterns, Binary Search on Answer Space
• Advanced graph algorithms

#### Giai đoạn 4 - Backend Special (7 ngày)
• LRU/LFU Cache implementation, Rate limiter designs
• Top-K problems, Scheduler problems

#### Giai đoạn 5 - Practice Sets
• Blind 75 fundamentals, NeetCode 150 comprehensive
• Striver SDE backend-focused, Company-specific problems

### Resources

#### Books
• **[Grokking Algorithms](pdf/Grokking-Algorithms.pdf)** - Visual approach, great for beginners
• **[The Algorithm Design Manual](pdf/The-Algorithm-Design-Manual-3rd-Edition.pdf)** - Practical algorithm design
• **[Introduction to Algorithms](pdf/Introduction-To-Algorithms-3rd-Edition.pdf)** - Comprehensive reference

#### Practice Sets Bắt Buộc
• [Blind 75](https://www.techinterviewhandbook.org/best-practice-questions/) - Core fundamentals
• [NeetCode 150](https://neetcode.io/practice?tab=neetcode150) - Comprehensive coverage
• [Striver SDE](https://takeuforward.org/interviews/strivers-sde-sheet-top-coding-interview-problems/) - Interview-focused

#### Online Platforms
• **LeetCode** - Most comprehensive, company-specific problems
• **NeetCode** - Curated problems với video explanations
• **HackerRank** - Interview practice và contests
• **Pramp** - Mock interviews với peers

## Complexity Cheat Sheet

### Time Complexities Cần Nhớ
• HashMap: O(1) avg, O(n) worst • Binary search: O(log n)
• Heap ops: O(log n) • Sorting: O(n log n) • DFS/BFS: O(V + E)
• Trie ops: O(m) với m = độ dài word • Union-Find: O(α(n)) ≈ O(1) amortized

### Space Complexities
• Recursion depth: O(height) trees • Graph algorithms: O(V) visited
• DP problems: Often O(n) hoặc O(n²)

## 7. 📖 Hướng Dẫn Ôn Tập & Cách Đọc File

### Cách Đọc File Theo Từng Vòng

#### Vòng 1 - Đọc tổng quan (1-2 ngày)
Đọc **toàn bộ file 1 lượt**, không cần nhớ chi tiết. Mục đích: có bản đồ tư duy DSA.
• Highlight các ⭐ và 🔥 — đó là phần "phải biết"
• Note lại phần hoàn toàn lạ (Skip List, HyperLogLog...) → để giai đoạn sau

#### Vòng 2 - Đọc sâu Section 1-3 (Core DSA)
Đọc xong **mỗi mục → code luôn 1 bài LeetCode** tương ứng. KHÔNG đọc lý thuyết suông.

**Mapping section → bài tập gợi ý:**
• **HashMap/HashSet** → Two Sum, Group Anagrams, Longest Consecutive
• **Linear (Stack/Queue)** → Valid Parentheses, Min Stack, Daily Temperatures
• **Two Pointers** → Container With Most Water, 3Sum
• **Sliding Window** → Longest Substring Without Repeat, Min Window Substring
• **Binary Search** → Search Rotated Array, Find Min in Rotated
• **Tree DFS/BFS** → Max Depth, Level Order, LCA
• **Graph** → Number of Islands, Course Schedule (topo sort)
• **Heap** → Kth Largest, Top K Frequent, Merge K Sorted Lists
• **Backtracking** → Subsets, Permutations, Word Search
• **DP** → Climbing Stairs, House Robber, LIS, Coin Change

#### Vòng 3 - Section 4 (Backend-specific)
Điểm khác biệt cho phỏng vấn backend. Không chỉ đọc — phải **tự implement từ đầu**:
1. **LRU Cache** (LeetCode 146) — bắt buộc thuộc lòng
2. **LFU Cache** (LeetCode 460)
3. **Rate Limiter** — design Token Bucket + Sliding Window
4. **Top-K Frequent Elements** (LeetCode 347) — 2 approach: heap & quickselect
5. **Consistent Hashing** — code ring + virtual nodes

---

### Lịch Ôn Hàng Ngày - Quy Tắc 3-2-1

Bám sát lộ trình 5 giai đoạn ở Section 6, mỗi ngày áp dụng:
• **3 bài mới** - 1 dễ, 1 trung, 1 khó của pattern hôm đó
• **2 bài ôn lại** - đã giải 2-7 ngày trước
• **1 bài review** - đọc lại solution cũ, viết lại từ đầu không nhìn

### Spaced Repetition
Bài đã giải → giải lại sau: **1 ngày → 3 ngày → 7 ngày → 14 ngày**

---

### Cách Dùng File Khi Luyện Bài

#### Trước khi giải bài
• Đọc đề → **KHÔNG vào code ngay**
• Mở section **"Pattern Recognition Framework"** → match keywords với pattern
• Nhìn **"Big O Reference"** → xác định target complexity

#### Khi bị stuck
• Mở section **"Khi bị stuck"** — làm theo đúng 4 bước
• Vẫn không ra → đọc hint, KHÔNG đọc solution

#### Sau khi giải xong
• Đối chiếu với **"Edge Cases Checklist"**
• Tự hỏi: complexity có khớp với **"Time Complexities Cần Nhớ"** không?
• Giải thích lại bằng lời (thinking aloud như trong interview)

---

### Mẹo Ôn Hiệu Quả

• **Từ pattern → bài tập**, không ngược lại. Tránh làm random bài trên LeetCode
• **Đừng skip brute force** - interviewer muốn thấy tư duy, không phải solution sẵn
• **Code trên giấy tuần cuối** trước phỏng vấn - mô phỏng whiteboard
• **Mock interview** (Pramp) 1-2 lần/tuần từ Giai đoạn 3 trở đi
• **Explain out loud** khi giải - rèn communication skill

---

### Tín Hiệu Sẵn Sàng Phỏng Vấn

✅ Nhìn đề là biết pattern trong ~1 phút
✅ Code LRU Cache trong 15 phút không lỗi
✅ Giải Blind 75 với ≥ 80% bài không cần hint
✅ Giải thích được complexity mọi bài mình làm
✅ Tự tin code trên whiteboard/giấy không cần IDE

---

### Anti-Patterns Cần Tránh

❌ Học thuộc solution mà không hiểu pattern
❌ Làm 500 bài mà không review lại
❌ Nhảy vào code ngay khi chưa clarify requirement
❌ Bỏ qua phân tích time/space complexity
❌ Chỉ làm bài dễ để tăng số lượng
❌ Không tự implement mà chỉ đọc solution

