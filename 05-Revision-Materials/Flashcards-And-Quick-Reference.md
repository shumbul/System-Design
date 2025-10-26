# 🧠 System Design Revision Materials

*"Repetition is the mother of learning, the father of action, which makes it the architect of accomplishment."*

## 📋 Quick Reference Checklists

### **System Design Interview Checklist** ✅
```
□ STEP 1: CLARIFY REQUIREMENTS (5-10 minutes)
  □ Ask about functional requirements
    □ What features does the system need?
    □ Who are the users?
    □ What is the primary use case?
  
  □ Ask about non-functional requirements
    □ How many users?
    □ How much data?
    □ What's the expected load?
    □ What's the expected response time?
    □ What's the availability requirement?
  
  □ Ask about constraints
    □ Budget limitations?
    □ Time constraints?
    □ Technology preferences?

□ STEP 2: ESTIMATE SCALE (5 minutes)
  □ Calculate daily active users
  □ Estimate requests per second
  □ Calculate storage requirements
  □ Estimate bandwidth needs
  □ Determine read vs write ratio

□ STEP 3: HIGH-LEVEL DESIGN (10-15 minutes)  
  □ Draw basic components
    □ Users/Clients
    □ Load Balancer
    □ Web Servers
    □ Application Servers
    □ Databases
  
  □ Show data flow
  □ Identify major services
  □ Keep it simple and clear

□ STEP 4: DETAILED DESIGN (15-20 minutes)
  □ Design database schema
  □ Define API endpoints
  □ Detail key algorithms
  □ Explain component interactions
  □ Address data consistency

□ STEP 5: SCALE THE DESIGN (10-15 minutes)
  □ Identify bottlenecks
  □ Add caching layers
  □ Implement load balancing
  □ Consider database scaling
  □ Plan for failure scenarios

□ STEP 6: MONITORING & WRAP-UP (5 minutes)
  □ Define key metrics
  □ Discuss trade-offs made
  □ Suggest improvements
  □ Address follow-up questions
```

### **Database Design Checklist** 🗄️
```
□ REQUIREMENTS ANALYSIS
  □ Identify entities and relationships
  □ Determine query patterns
  □ Estimate data volume and growth
  □ Define consistency requirements

□ SQL vs NoSQL DECISION
  □ Need ACID transactions? → SQL
  □ Need complex queries/joins? → SQL
  □ Need flexible schema? → NoSQL
  □ Need horizontal scaling? → NoSQL

□ SCHEMA DESIGN
  □ Design tables/collections
  □ Define primary and foreign keys
  □ Plan for data normalization/denormalization
  □ Consider future schema evolution

□ PERFORMANCE OPTIMIZATION
  □ Add appropriate indexes
  □ Plan for query optimization
  □ Consider partitioning/sharding
  □ Design caching strategy

□ SCALING STRATEGY
  □ Plan for read replicas
  □ Design sharding key
  □ Consider geographic distribution
  □ Plan backup and recovery
```

### **Caching Strategy Checklist** ⚡
```
□ CACHE PLACEMENT
  □ Browser cache for static assets
  □ CDN for global content delivery
  □ Application cache for computed results
  □ Database query cache

□ CACHE PATTERNS
  □ Cache-aside for read-heavy workloads
  □ Write-through for consistency needs
  □ Write-behind for write-heavy workloads
  □ Refresh-ahead for predictable access

□ CACHE CONSIDERATIONS
  □ Define cache TTL (Time To Live)
  □ Plan cache invalidation strategy
  □ Handle cache miss scenarios
  □ Monitor cache hit rates

□ CACHE TECHNOLOGIES
  □ Redis for complex data structures
  □ Memcached for simple key-value
  □ Application-level caching
  □ Database query caching
```

## 🃏 System Design Flashcards

### **Core Concepts** 💭
```
CARD 1: SCALABILITY
Front: What is the difference between vertical and horizontal scaling?
Back: 
- Vertical (Scale Up): Add more power to existing machine (CPU, RAM)
- Horizontal (Scale Out): Add more machines to the resource pool
- Vertical is easier but has limits; Horizontal is more complex but infinitely scalable

CARD 2: CAP THEOREM
Front: What does CAP Theorem state?
Back:
In a distributed system, you can only guarantee 2 out of 3:
- Consistency: All nodes see the same data
- Availability: System remains operational
- Partition tolerance: System continues despite network failures

CARD 3: ACID PROPERTIES
Front: What are ACID properties in databases?
Back:
- Atomicity: All or nothing transactions
- Consistency: Valid state to valid state
- Isolation: Concurrent transactions don't interfere
- Durability: Committed changes persist

CARD 4: LOAD BALANCING
Front: Name 3 load balancing algorithms
Back:
- Round Robin: Requests distributed evenly in sequence
- Least Connections: Route to server with fewest active connections
- Weighted Round Robin: Distribute based on server capacity

CARD 5: DATABASE SHARDING
Front: What is database sharding and when to use it?
Back:
Sharding = Horizontal partitioning of data across multiple databases
Use when: Single database can't handle load, need to scale writes
Methods: Range-based, Hash-based, Geographic
```

### **System Components** 🔧
```
CARD 6: MICROSERVICES vs MONOLITH
Front: When to choose microservices over monolith?
Back:
Choose Microservices when:
- Team size > 10 people
- Need independent deployments
- Different scaling requirements per service
- Technology diversity needed

Choose Monolith when:
- Small team (< 10 people)
- Simple application
- Rapid prototyping
- Uncertain requirements

CARD 7: MESSAGE QUEUES
Front: What problems do message queues solve?
Back:
- Decoupling: Services don't need to know about each other
- Reliability: Messages persist if consumer is down
- Scalability: Multiple consumers can process messages
- Load Leveling: Handle traffic spikes

CARD 8: CDN (Content Delivery Network)
Front: How does a CDN improve performance?
Back:
- Geographic distribution: Content served from nearest location
- Caching: Frequently accessed content cached at edge
- Reduced latency: Shorter network distance
- Origin offloading: Reduces load on main servers

CARD 9: DATABASE REPLICATION
Front: Master-Slave vs Master-Master replication?
Back:
Master-Slave:
- One write node, multiple read nodes
- Simple, good for read-heavy workloads
- Single point of failure for writes

Master-Master:
- Multiple write nodes
- No single point of failure
- Complex conflict resolution needed
```

### **Design Patterns** 🎨
```
CARD 10: CACHE PATTERNS
Front: Explain Cache-Aside pattern
Back:
Read: Check cache → If miss, read from DB → Store in cache
Write: Write to DB → Invalidate cache
Pros: Simple, handles cache misses gracefully
Cons: Potential for inconsistent data

CARD 11: API GATEWAY PATTERN
Front: What is an API Gateway and its benefits?
Back:
Single entry point for all client requests
Benefits:
- Authentication/Authorization centralized
- Rate limiting and throttling
- Request/Response transformation
- Load balancing and routing
- Monitoring and analytics

CARD 12: CIRCUIT BREAKER PATTERN
Front: How does Circuit Breaker pattern work?
Back:
Three states:
- Closed: Normal operation
- Open: Fail fast, don't call failing service
- Half-Open: Test if service recovered
Prevents cascade failures in distributed systems
```

## 📊 Quick Reference Sheets

### **Latency Numbers Every Programmer Should Know** ⏱️
```
┌─────────────────────────────────┬──────────────┐
│ Operation                       │ Latency      │
├─────────────────────────────────┼──────────────┤
│ L1 cache reference              │ 0.5 ns       │
│ Branch mispredict               │ 5 ns         │
│ L2 cache reference              │ 7 ns         │
│ Mutex lock/unlock               │ 25 ns        │
│ Main memory reference           │ 100 ns       │
│ Compress 1K with Zippy          │ 10,000 ns    │
│ Send 1K over 1 Gbps network     │ 10,000 ns    │
│ Read 4K randomly from SSD       │ 150,000 ns   │
│ Read 1 MB sequentially from mem │ 250,000 ns   │
│ Round trip within datacenter    │ 500,000 ns   │
│ Read 1 MB from SSD              │ 1,000,000 ns │
│ Disk seek                       │ 10,000,000 ns│
│ Read 1 MB from network          │ 10,000,000 ns│
│ Read 1 MB from disk             │ 30,000,000 ns│
│ Send packet CA→Netherlands→CA   │150,000,000 ns│
└─────────────────────────────────┴──────────────┘

Key Takeaways:
- Memory is 100x faster than network
- SSD is 100x faster than disk
- Same datacenter is 300x faster than cross-continent
```

### **Powers of Two Table** 🔢
```
┌──────────┬─────────────┬─────────────────────────┐
│ Power    │ Exact Value │ Approx Value            │
├──────────┼─────────────┼─────────────────────────┤
│ 2^10     │ 1,024       │ 1 thousand (1K)         │
│ 2^16     │ 65,536      │ 65K                     │
│ 2^20     │ 1,048,576   │ 1 million (1M)          │
│ 2^30     │ 1,073,741,824 │ 1 billion (1B)        │
│ 2^32     │ 4,294,967,296 │ 4 billion             │
│ 2^40     │ 1,099,511,627,776 │ 1 trillion (1T)   │
└──────────┴─────────────┴─────────────────────────┘

Memory/Storage Sizes:
- 1 byte = 8 bits
- 1 KB = 1,024 bytes ≈ 1,000 bytes
- 1 MB = 1,024 KB ≈ 1 million bytes
- 1 GB = 1,024 MB ≈ 1 billion bytes
- 1 TB = 1,024 GB ≈ 1 trillion bytes
```

### **HTTP Status Codes Reference** 🌐
```
┌─────────┬────────────────────────────────────────┐
│ Code    │ Meaning & When to Use                  │
├─────────┼────────────────────────────────────────┤
│ 200 OK  │ Request successful                     │
│ 201     │ Resource created successfully          │
│ 204     │ No content (successful DELETE)         │
│ 400     │ Bad request (client error)             │
│ 401     │ Unauthorized (authentication required) │
│ 403     │ Forbidden (no permission)              │
│ 404     │ Not found                              │
│ 409     │ Conflict (resource already exists)     │
│ 429     │ Too many requests (rate limited)       │
│ 500     │ Internal server error                  │
│ 502     │ Bad gateway (upstream server error)    │
│ 503     │ Service unavailable (overloaded)       │
│ 504     │ Gateway timeout                        │
└─────────┴────────────────────────────────────────┘
```

## 🎯 Practice Questions by Difficulty

### **Beginner Questions** 🌱
```
1. Design a URL Shortener (like bit.ly)
   Focus Areas: Database design, hashing, caching
   Expected Duration: 30-40 minutes
   Key Components: Web server, database, cache
   
2. Design a Pastebin (like pastebin.com)  
   Focus Areas: Text storage, simple CRUD operations
   Expected Duration: 25-35 minutes
   Key Components: Web server, database, file storage

3. Design a Chat Application
   Focus Areas: Real-time messaging, WebSockets
   Expected Duration: 35-45 minutes
   Key Components: WebSocket server, message queue, database

4. Design a Social Media Feed
   Focus Areas: Timeline generation, following relationships
   Expected Duration: 40-50 minutes
   Key Components: User service, post service, timeline cache

5. Design a File Storage System (like Dropbox basic)
   Focus Areas: File upload/download, metadata management
   Expected Duration: 35-45 minutes
   Key Components: File storage, metadata database, CDN
```

### **Intermediate Questions** 🚀
```
6. Design Twitter
   Focus Areas: Timeline generation, fanout strategies, caching
   Expected Duration: 45-60 minutes
   Key Challenges: Celebrity user problem, real-time updates

7. Design Instagram
   Focus Areas: Photo storage, feed generation, following graph
   Expected Duration: 45-60 minutes  
   Key Challenges: Media storage, image processing, mobile optimization

8. Design WhatsApp
   Focus Areas: Real-time messaging, message delivery, encryption
   Expected Duration: 50-60 minutes
   Key Challenges: Message ordering, offline message delivery

9. Design Uber/Lyft
   Focus Areas: Real-time location tracking, matching algorithm
   Expected Duration: 50-60 minutes
   Key Challenges: Geo-spatial indexing, real-time updates

10. Design Netflix
    Focus Areas: Video streaming, content recommendation, CDN
    Expected Duration: 50-60 minutes
    Key Challenges: Video encoding, global distribution
```

### **Advanced Questions** 🏆
```
11. Design Google Search
    Focus Areas: Web crawling, indexing, ranking algorithms
    Expected Duration: 60+ minutes
    Key Challenges: Scale of web, freshness vs relevance

12. Design Amazon E-commerce
    Focus Areas: Product catalog, order processing, payments
    Expected Duration: 60+ minutes
    Key Challenges: Inventory management, transaction consistency

13. Design YouTube
    Focus Areas: Video upload/processing, global CDN, recommendations
    Expected Duration: 60+ minutes
    Key Challenges: Video transcoding, storage optimization

14. Design Facebook Messenger
    Focus Areas: Real-time messaging at scale, group chats, multimedia
    Expected Duration: 60+ minutes
    Key Challenges: Message synchronization, mobile optimization

15. Design Distributed Cache
    Focus Areas: Consistent hashing, replication, fault tolerance
    Expected Duration: 60+ minutes
    Key Challenges: Data consistency, cache coherence
```

## 🧪 Mock Interview Scenarios

### **Common Follow-up Questions** 🤔
```
After Initial Design:

SCALABILITY QUESTIONS:
- "What if we have 10x more users?"
- "How would you handle traffic spikes?"
- "What happens when database becomes bottleneck?"

RELIABILITY QUESTIONS:
- "What if a server crashes?"
- "How do you handle network partitions?"
- "What's your disaster recovery plan?"

PERFORMANCE QUESTIONS:
- "How would you reduce latency?"
- "What if response time is too slow?"
- "How do you optimize for mobile users?"

CONSISTENCY QUESTIONS:
- "What if two users update same data simultaneously?"
- "How do you handle eventual consistency?"
- "What are the trade-offs of your consistency model?"

SECURITY QUESTIONS:
- "How do you prevent DDoS attacks?"
- "How do you handle user authentication?"
- "What about data privacy and encryption?"
```

### **Red Flags to Avoid** 🚫
```
DESIGN RED FLAGS:
❌ Jumping to implementation details too early
❌ Not asking clarifying questions
❌ Ignoring non-functional requirements
❌ Over-engineering for day one
❌ Not considering failure scenarios

COMMUNICATION RED FLAGS:
❌ Not thinking out loud
❌ Getting defensive about design choices
❌ Not explaining trade-offs
❌ Rushing through the design
❌ Not engaging with interviewer feedback

TECHNICAL RED FLAGS:
❌ Using buzzwords without understanding
❌ Not understanding chosen technologies
❌ Inconsistent architectural decisions
❌ Ignoring data consistency issues
❌ Not considering operational complexity
```

## 📚 Study Schedule Templates

### **4-Week Intensive Plan** 🏃‍♂️
```
WEEK 1: FOUNDATIONS
Day 1-2: Read system design fundamentals
Day 3-4: Study database design patterns
Day 5-6: Learn caching strategies
Day 7: Practice: Design URL shortener

WEEK 2: COMPONENTS & PATTERNS
Day 8-9: Microservices vs monolith
Day 10-11: Load balancing and CDN
Day 12-13: Message queues and real-time systems
Day 14: Practice: Design chat application

WEEK 3: REAL-WORLD SYSTEMS
Day 15-16: Study Twitter architecture
Day 17-18: Study WhatsApp architecture  
Day 19-20: Study Netflix architecture
Day 21: Practice: Design social media feed

WEEK 4: INTERVIEW PREPARATION
Day 22-23: Mock interviews with peers
Day 24-25: Review weak areas
Day 26-27: Practice advanced problems
Day 28: Final review and confidence building
```

### **12-Week Comprehensive Plan** 📖
```
PHASE 1: FOUNDATIONS (Weeks 1-4)
- Week 1: Basic concepts and terminology
- Week 2: Database design and scaling
- Week 3: Caching and performance optimization
- Week 4: Load balancing and CDN

PHASE 2: SYSTEM COMPONENTS (Weeks 5-8)
- Week 5: Microservices architecture
- Week 6: Message queues and async processing
- Week 7: Real-time systems and WebSockets
- Week 8: Security and authentication

PHASE 3: REAL-WORLD SYSTEMS (Weeks 9-11)
- Week 9: Social media systems (Twitter, Instagram)
- Week 10: Messaging systems (WhatsApp, Slack)
- Week 11: Streaming systems (Netflix, YouTube)

PHASE 4: MASTERY (Week 12)
- Interview practice and mock sessions
- Review and strengthen weak areas
- Advanced topics and edge cases
```

---

*"Success in system design comes not from memorizing solutions, but from understanding principles and practicing their application to new problems."*