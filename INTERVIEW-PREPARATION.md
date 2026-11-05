# Interview Preparation Guide

> Strategic approach to senior software engineering interviews

## 🎯 Interview Types & Strategy

### 1. Coding Interviews (45-60 min)
**Format:** 1-2 algorithm/data structure problems
**Strategy:**
- Start with brute force → optimize
- Think aloud, explain approach before coding
- Test with examples, handle edge cases
- Analyze time/space complexity

**Key areas for backend roles:**
- Arrays, strings, hashmaps (most common)
- Trees, graphs (system topology thinking)
- Caching problems (LRU, LFU)
- Rate limiters, schedulers

### 2. System Design Interviews (45-60 min)
**Format:** Design a large-scale system (Twitter, YouTube, Chat)
**Strategy:**
- Follow the **[5-step system design process](2-Design-And-Architecture/design-and-architecture.md#7-system-design-process-memorize-this)** from Design & Architecture notes
- Focus on trade-offs, not perfect solutions
- Discuss bottlenecks and how to resolve them
- Show distributed systems knowledge

**Common topics:**
- URL shortener, Social media feed, Chat system
- Video streaming, Search engine, Ride-sharing
- Payment systems, Notification service

### 3. Technical Deep-Dive (30-45 min)
**Format:** Deep discussion on experience, technologies
**Strategy:**
- Prepare 2-3 detailed project stories
- Know your technology stack deeply
- Explain trade-offs in technical decisions
- Discuss performance optimizations, debugging

### 4. Behavioral Interviews (30-45 min)
**Format:** Leadership, collaboration, problem-solving scenarios
**Strategy:**
- Use STAR method (Situation, Task, Action, Result)
- Prepare stories showing: leadership, conflict resolution, technical challenges
- Show growth mindset, learning from failures

---

## 🧠 Senior-Level Interview Excellence

### 🎯 System Design Mastery
**✅ Senior Engineer Approach:**
- **Start with observability:** Discuss monitoring, logging, and metrics upfront
- **Plan for failures:** Address fault tolerance, recovery scenarios, and degraded states
- **Think operationally:** Consider team scalability, deployment complexity, and maintenance
- **Evaluate trade-offs:** Weigh complexity vs benefits, discuss alternatives

**❌ Common Junior Pitfalls:**
- Jumping to microservices architecture without justification
- Ignoring data layer design and consistency requirements
- Forgetting about monitoring and operational concerns
- Over-engineering solutions without considering maintenance burden

### 🏆 Technical Leadership Signals
**Demonstrate Deep Understanding:**
- Explain technology choices with trade-offs and alternatives
- Discuss performance characteristics, bottlenecks, and optimizations
- Show awareness of failure modes and mitigation strategies
- Reference specific metrics and business impact

**Show Senior Leadership:**
- Technical decisions that affected team productivity
- Mentoring experiences and knowledge transfer
- Balancing technical debt vs feature delivery
- Cross-team collaboration and architecture decisions

### ⚠️ Critical Pitfalls to Avoid
**Coding Interviews:**
- Jumping straight to code without clarifying requirements
- Not testing with examples or considering edge cases
- Ignoring time/space complexity analysis

**System Design:**
- Focusing on implementation details too early
- Ignoring scalability considerations until the end
- Not asking clarifying questions about scale and constraints
- Making unrealistic assumptions about user load or data volume

---

## 📋 Interview Day Checklist

### Before the Interview
- [ ] Test tech setup (camera, mic, screen share)
- [ ] Prepare pen/paper for notes and diagrams
- [ ] Review company's engineering blog
- [ ] Prepare 3-5 thoughtful questions to ask

### During Coding Interviews
- [ ] Clarify requirements and constraints
- [ ] Start with brute force approach
- [ ] Think aloud while coding
- [ ] Test with examples and edge cases
- [ ] Analyze time/space complexity

### During System Design
- [ ] Follow the **[memorized 5-step process](2-Design-And-Architecture/design-and-architecture.md#7-system-design-process-memorize-this)**
- [ ] Apply scaling patterns (caching, load balancing, partitioning) from architecture notes
- [ ] Consider failure scenarios and observability
- [ ] Discuss trade-offs at each step

### Questions to Ask Interviewers
**Technical:**
- What's the biggest technical challenge the team faces?
- How do you handle technical debt?
- What's your deployment/testing process?

**Team & Culture:**
- How do you mentor junior engineers?
- What does career growth look like here?
- How are technical decisions made?

---

## 📚 Essential Resources

### Books (Priority Order)
1. [System Design Interview](2-Design-And-Architecture/pdf/System-Design-Interview.pdf) - **Must read**
2. [Cracking the Coding Interview](3-Backend-Technology-Stack/pdf/Cracking-the-Coding-Interview-6th-Edition.pdf)
3. [Elements of Programming Interviews in Java](3-Backend-Technology-Stack/pdf/Elements-of-Programming-Interviews-In-Java.pdf)

### Online Practice
**Coding:**
- [LeetCode](https://leetcode.com/) - Practice problems
- [NeetCode](https://neetcode.io/) - Curated problems with video explanations

**System Design:**
- [System Design Interview Questions](https://github.com/donnemartin/system-design-primer)
- [Grokking System Design](https://www.educative.io/courses/grokking-the-system-design-interview)

**Mock Interviews:**
- [Pramp](https://www.pramp.com/) - Free peer practice
- [Interviewing.io](https://interviewing.io/) - Anonymous practice with engineers

### Company Research
- **Engineering blogs:** [High Scalability](http://highscalability.com/), company engineering blogs
- **Architecture talks:** YouTube channels, conference presentations from target companies
- **Recent developments:** Latest tech stack changes, new product launches, technical challenges

---

## Behavioral Interview — STAR Framework

### STAR Structure
- **Situation:** Bối cảnh, khi nào xảy ra
- **Task:** Nhiệm vụ của bạn, trách nhiệm cụ thể
- **Action:** Những gì BẠN đã làm (không phải team, dùng "I")
- **Result:** Kết quả đo lường được, impact thực tế

### Essential STAR Stories (chuẩn bị ít nhất 1 cho mỗi chủ đề)

#### Technical Challenge
**S:** Payment service có latency spike đột ngột lên P99 = 8s trong production
**T:** Tôi phụ trách investigate và fix trong 24h (SLA với merchant)
**A:**
- Query profiling → phát hiện N+1 query trong order history endpoint
- Thêm batch loading + DB index → query time giảm từ 400ms → 15ms
- Add Redis cache cho repeated queries (TTL 5 phút)
- Deploy với canary (5% traffic) → monitor 30 phút → roll out 100%

**R:** P99 latency giảm từ 8s → 180ms, 0 customer complaints, giải pháp applied cho 3 similar endpoints khác

#### Conflict / Disagreement
**S:** Code review: tôi và senior dev bất đồng về caching strategy
**T:** Tôi propose Redis caching, họ argue không cần thiết
**A:**
- Tôi prepare benchmark data: load test results showing cache benefit
- Đề xuất A/B test: 1 tuần với/không có cache
- Trình bày trade-offs cụ thể (complexity vs performance gain)

**R:** Team đồng ý A/B test → cache giảm DB load 60% → adopted cho toàn bộ service

#### Leadership / Mentoring
**S:** Junior developer gặp khó khăn với Kafka consumer implementation
**T:** Tôi phụ trách onboarding và technical growth cho team member mới
**A:**
- Pair programming 2 buổi/tuần trong 1 tháng
- Tạo internal wiki với Kafka patterns và gotchas
- Code review với explanation, không chỉ correction

**R:** Junior dev tự implement full Kafka consumer sau 6 tuần. Wiki được team dùng như reference, giảm onboarding time 30%

#### Failure / What You Learned
**S:** Tôi deploy feature không test kỹ → gây outage 15 phút
**T:** Feature owner + on-call engineer
**A:**
- Immediate rollback (5 phút detect + rollback)
- Hotfix với proper testing
- Post-mortem: root cause là missing integration test
- Tôi propose và implement: mandatory integration test cho payment flows

**R:** Zero similar incidents trong 12 tháng tiếp theo. Lesson: speed không đáng đánh đổi với reliability cho critical paths

### Common Behavioral Questions

#### "Tell me about yourself"
Structure:
1. Current role + main responsibility (2 sentences)
2. Key achievement / impact (1–2 sentences)
3. Why you're excited about THIS role (1–2 sentences)

Example: "I'm a Senior Backend Engineer at [Company], leading the payments team where I'm responsible for processing X million transactions/month. Recently I led the migration from monolith to microservices, reducing deployment time from 2 hours to 15 minutes. I'm excited about this role because [specific reason about company/tech]."

#### "Why do you want to leave?"
Focus on positive (pull factors, không negative về công ty cũ):
- "Looking for more ownership / technical challenges"
- "Excited about [company]'s scale and technical problems"
- "Want to work on [specific technology/domain]"

Avoid: "My boss is bad", "Company is not stable"

#### "Where do you see yourself in 5 years?"
Align with the role:
- Technical track: "Staff/Principal Engineer, deeper system design expertise"
- Management track: "Engineering Manager, build and grow high-performance teams"
- Add: "I see this role as a great step toward that because [reason]"

---

## Vietnam/SEA Market Interview Specifics

### Common Tech Stack Questions for Vietnam Market
- Kafka consumer groups, offset management
- Redis clustering, Sentinel vs Cluster
- Kubernetes deployment trên AWS EKS
- Database partitioning / sharding cho Vietnamese scale
- Payment integration (VNPay, MoMo, Zalopay)

### Interview Formats in Vietnam

#### Technical Screen (45-60 phút)
- LeetCode Medium (data structures, algorithms)
- System design basics
- Java/Spring knowledge questions

#### Technical Interview (90-120 phút)
- LeetCode Medium-Hard hoặc live coding
- System design (design a payment system, ride-matching, URL shortener)
- Deep-dive past experience

#### Culture/Fit Interview
- Behavioral questions
- Team dynamics, working style
- Vietnamese companies thường focus: ownership, learning mindset, stability

