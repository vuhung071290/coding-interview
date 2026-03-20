# Concurrency

---

# 1. Khái niệm nền tảng

## 1.1 Concurrency và Parallelism
- Concurrency: tổ chức xử lý nhiều công việc tiến triển cùng lúc.
- Parallelism: chạy nhiều công việc đồng thời thực sự trên nhiều core.
- Concurrency là tổ chức; Parallelism là thực thi vật lý.

## 1.2 Multithreading và Multiprocessing
- Thread: chia sẻ bộ nhớ, nhanh nhưng dễ race.
- Process: tách bộ nhớ, an toàn hơn nhưng tốn chi phí.

## 1.3 Asynchronous và Multithreading
- Async/await: một thread, non-blocking, tối ưu I/O-bound.
- Multithreading: nhiều thread thật, tối ưu CPU-bound.
- Kết luận nhanh:
    - I/O-bound → async
    - CPU-bound → thread/process

---

# 2. Execution Models

## 2.1 Thread-based
- Chia sẻ bộ nhớ
- Cần lock để tránh race

## 2.2 Process-based
- Isolation tốt
- IPC tốn chi phí

## 2.3 Event loop
- Một thread
- Non-blocking I/O
- Dùng cho server I/O cao

## 2.4 Coroutine
- Nhẹ
- Cooperative scheduling
- Tối ưu I/O-bound

## 2.5 Actor / CSP
- Không chia sẻ memory
- Giao tiếp qua message
- An toàn hơn lock-based

---

# 3. Synchronization

## 3.1 Các vấn đề cần tránh
- Race condition
- Deadlock
- Livelock
- Starvation

## 3.2 Công cụ
- Mutex/Lock
- Semaphore
- Read–Write Lock
- Condition Variable
- Atomic/CAS
- Barrier/Latch

## 3.3 Cách làm thread-safe
- Lock-based
- Lock-free
- Immutable
- Thread-local
- Concurrent collections

---

# 4. Memory Model

- Atomicity: thao tác không chia nhỏ
- Visibility: thread khác có thấy thay đổi không
- Ordering: tránh reordering không mong muốn
- volatile: đảm bảo visibility + ordering

---

# 5. Asynchronous Programming

- Event loop: reactor pattern, non-blocking I/O
- Async/await: code tuần tự, chạy bất đồng bộ
- Reactive programming: xử lý theo stream

---

# 6. Performance

## 6.1 Chi phí chính
- Context switching
- Lock contention
- Blocking trong async
- False sharing

## 6.2 Tối ưu
- Giảm blocking
- Giảm thời gian giữ lock
- Thread pool
- Tách shared state
- Tránh false sharing

---

# 7. Concurrency Patterns

- Producer–Consumer
- Thread Pool
- Pipeline
- Fan-in / Fan-out
- Fork–Join
- Future / Promise
- Publish–Subscribe
- Actor model
- Timeout / Cancellation

---

# 8. Chọn mô hình theo workload

- CPU-bound → thread pool, multiprocessing
- I/O-bound → async, event loop
- High concurrency → Go, virtual threads, actor
- Fault-tolerant → actor
- Shared memory phức tạp → STM hoặc lock-based rõ ràng

---

# 9. Use Cases

- Web server → async
- Data pipeline → CSP, reactive
- Simulation/game → thread pool
- Messaging → actor
- Microservices → async + worker

---

# 10. Bộ câu hỏi ôn tập

- Concurrency khác parallelism?
- Thread vs process?
- Async khác thread?
- Event loop hoạt động ra sao?
- Mutex khác semaphore?
- Happens-before là gì?
- False sharing là gì?
- Producer–consumer hoạt động thế nào?
- CPU-bound nên dùng gì?

---

# 11. Structured Concurrency

## 11.1 Khái niệm
- Task phải có phạm vi rõ ràng
- Parent chờ và quản lý toàn bộ child
- Không để task chạy nền và bị rò rỉ

## 11.2 Lợi ích
- Tránh task leak
- Dễ debug
- Cancellation và exception lan truyền rõ ràng

## 11.3 Ví dụ ngôn ngữ
- Kotlin: coroutineScope
- Python Trio: nursery
- Java Loom: StructuredTaskScope

---

# 12. Cancellation và Timeout

## 12.1 Cancellation
- Cooperative: task tự kiểm tra signal và dừng
- Không force-kill

## 12.2 Timeout
- Mọi I/O cần timeout: HTTP, DB, lock, external API

## 12.3 Tại sao quan trọng
- Tránh blocking vô hạn
- Giữ hệ thống an toàn và responsive
- Ngăn lỗi lan truyền

## 12.4 Best practices
- Truyền cancellation token
- Cancellation lan xuống child
- Timeout đặt tại tầng cao nhất

---

# Tóm tắt nhanh 20 giây

- Concurrency là tổ chức xử lý nhiều việc
- Parallelism là chạy nhiều việc cùng lúc
- I/O-bound → async; CPU-bound → thread
- Vấn đề chính: race, deadlock, visibility, ordering
- Công cụ: lock, semaphore, atomic
- Mô hình: thread, coroutine, actor, CSP
- Tối ưu: giảm blocking, contention, context switching, false sharing
- Mô hình hiện đại: structured concurrency và cancellation/timeout  
