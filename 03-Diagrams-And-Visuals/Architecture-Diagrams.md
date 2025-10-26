# 🎨 System Design Visual Guide

*"A picture is worth a thousand lines of code"*

## 👀 How to Read These Diagrams (Start Here!)

### **Diagram Reading Guide for Beginners** 📖
```
Before diving into complex diagrams, let's learn the "language":

📦 Boxes = Components (servers, databases, services)
↔️ Arrows = Data flow (information moving between components)
🔄 Double arrows = Two-way communication
│ Vertical lines = Connections
┌─┐ Box corners = System boundaries
⚡ Lightning = Fast operations
💾 Database icon = Where data is stored
🌐 Globe = Internet/Network
```

### **The Progressive Learning Approach** 📚
```
Level 1: Identify the boxes (What are the main parts?)
Level 2: Follow the arrows (How does data flow?)
Level 3: Understand the "why" (Why is it designed this way?)
Level 4: Spot the patterns (Where have I seen this before?)
Level 5: Find the trade-offs (What are the pros and cons?)

Don't try to understand everything at once!
```

### **Common Diagram Symbols** 🔤
```
👤 = User/Client
💻 = Server/Computer  
🗄️ = Database
⚖️ = Load Balancer
⚡ = Cache
🌐 = Internet/CDN
📱 = Mobile App
🖥️ = Web Browser
🔒 = Security/Authentication
📊 = Analytics/Monitoring
```

## 🏗️ Basic Architecture Patterns

### **Monolithic Architecture** 🏢
```
┌─────────────────────────────────────────┐
│             MONOLITHIC APP              │
├─────────────────────────────────────────┤
│  ┌───────────┐ ┌───────────┐ ┌────────┐ │
│  │   User    │ │  Payment  │ │ Order  │ │
│  │ Management│ │ Processing│ │ System │ │
│  └───────────┘ └───────────┘ └────────┘ │
├─────────────────────────────────────────┤
│              Shared Database            │
└─────────────────────────────────────────┘

Pros: ✅ Simple deployment, Easy testing
Cons: ❌ Hard to scale parts independently
```

### **Microservices Architecture** 🏘️
```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│    USER     │    │   PAYMENT   │    │    ORDER    │
│   SERVICE   │    │   SERVICE   │    │   SERVICE   │
├─────────────┤    ├─────────────┤    ├─────────────┤
│   📊 DB     │    │   💳 DB     │    │   📦 DB     │
└─────────────┘    └─────────────┘    └─────────────┘
       │                   │                   │
       └─────────────── API Gateway ──────────┘
                           │
                    ┌─────────────┐
                    │    CLIENT   │
                    │     APP     │
                    └─────────────┘

Pros: ✅ Independent scaling, Technology diversity
Cons: ❌ Network complexity, Data consistency
```

## ⚖️ Load Balancer Patterns

### **Round Robin Load Balancing** 🔄
```
                    Client Requests
                         │
            ┌─────────────────────────┐
            │    LOAD BALANCER        │
            │    (Round Robin)        │
            └─────────────────────────┘
                    │  │  │
        ┌───────────┘  │  └───────────┐
        │              │              │
        ▼              ▼              ▼
   ┌─────────┐    ┌─────────┐    ┌─────────┐
   │SERVER 1 │    │SERVER 2 │    │SERVER 3 │
   │Load: 33%│    │Load: 33%│    │Load: 34%│
   └─────────┘    └─────────┘    └─────────┘

Request Flow:
Request 1 → Server 1
Request 2 → Server 2  
Request 3 → Server 3
Request 4 → Server 1 (cycle repeats)
```

### **Weighted Load Balancing** ⚖️
```
                    Client Requests
                         │
            ┌─────────────────────────┐
            │    LOAD BALANCER        │
            │    (Weighted 3:2:1)     │
            └─────────────────────────┘
                    │  │  │
        ┌───────────┘  │  └───────────┐
        │              │              │
        ▼              ▼              ▼
   ┌─────────┐    ┌─────────┐    ┌─────────┐
   │SERVER 1 │    │SERVER 2 │    │SERVER 3 │
   │16 cores │    │ 8 cores │    │ 4 cores │
   │Weight:3 │    │Weight:2 │    │Weight:1 │
   └─────────┘    └─────────┘    └─────────┘

Distribution: 50% → 33% → 17%
```

## 💾 Database Scaling Patterns

### **Master-Slave Replication** 👑👥
```
                    APPLICATION
                         │
              ┌──────────┼──────────┐
              │          │          │
              ▼          │          ▼
         ┌─────────┐     │     ┌─────────┐
         │  READ   │     │     │  READ   │
         │REPLICA 1│     │     │REPLICA 2│
         └─────────┘     │     └─────────┘
              │          │          │
              │    ┌─────────┐      │
              │    │ MASTER  │      │
              │    │DATABASE │      │
              │    │(WRITE)  │      │
              │    └─────────┘      │
              │          │          │
    ┌─────────┴──────────┼──────────┴─────────┐
    │     REPLICATION    │    REPLICATION     │
    │    (Async Copy)    │    (Async Copy)    │
    └────────────────────┼────────────────────┘

Read Queries: 80% → Distributed to replicas
Write Queries: 20% → All go to master
```

### **Database Sharding (Horizontal)** 🗂️
```
                    APPLICATION
                         │
              ┌──────────┼──────────┐
              │          │          │
              ▼          ▼          ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │   SHARD 1   │ │   SHARD 2   │ │   SHARD 3   │
    │  Users A-H  │ │  Users I-P  │ │  Users Q-Z  │
    │             │ │             │ │             │
    │ user_id %3  │ │ user_id %3  │ │ user_id %3  │
    │    = 0      │ │    = 1      │ │    = 2      │
    └─────────────┘ └─────────────┘ └─────────────┘

Sharding Logic:
hash(user_id) % 3 = shard_number

Example:
user_id 12345 → hash(12345) % 3 = 0 → Shard 1
user_id 67890 → hash(67890) % 3 = 1 → Shard 2
```

## ⚡ Caching Strategies

### **Cache Hierarchy** 🏔️
```
    CLIENT REQUEST
         │
         ▼
┌─────────────────┐     Cache Hit: 1ms
│  BROWSER CACHE  │ ◄── (Static files, images)
└─────────────────┘
         │ Miss
         ▼
┌─────────────────┐     Cache Hit: 10ms
│   CDN CACHE     │ ◄── (Global content delivery)
└─────────────────┘
         │ Miss
         ▼
┌─────────────────┐     Cache Hit: 1-5ms
│  REDIS CACHE    │ ◄── (Application data)
└─────────────────┘
         │ Miss
         ▼
┌─────────────────┐     Query Time: 100ms+
│    DATABASE     │ ◄── (Persistent storage)
└─────────────────┘

Cache Hit Ratios:
Browser: ~95%
CDN: ~90%
Redis: ~80%
Database: Always hit (last resort)
```

### **Cache-Aside Pattern** 🎯
```
    CLIENT
       │
       ▼
┌─────────────┐
│ APPLICATION │
└─────────────┘
   │       │
   ▼       ▼
┌──────┐ ┌────────┐
│CACHE │ │DATABASE│
└──────┘ └────────┘

Read Flow:
1. App checks cache
2. If HIT: Return data
3. If MISS: Query DB
4. Store result in cache
5. Return data to client

Write Flow:
1. App writes to DB
2. App invalidates cache
3. Next read will reload from DB
```

### **Write-Through Cache** ✍️
```
    CLIENT
       │
       ▼
┌─────────────┐
│ APPLICATION │
└─────────────┘
       │
       ▼
┌─────────────┐     Write to both
│    CACHE    │ ◄───────────┐
└─────────────┘             │
       │                    │
       ▼                    │
┌─────────────┐             │
│  DATABASE   │ ◄───────────┘
└─────────────┘

Write Flow:
1. App writes to cache AND database
2. Both must succeed
3. Cache always consistent with DB
4. Slower writes, faster reads
```

## 🔄 Message Queue Patterns

### **Producer-Consumer Pattern** 📬
```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  PRODUCER   │───▶│    QUEUE    │───▶│  CONSUMER   │
│   SERVICE   │    │   (KAFKA)   │    │   SERVICE   │
└─────────────┘    └─────────────┘    └─────────────┘
                        │
                   ┌────┴────┐
                   │ Message │
                   │ Message │
                   │ Message │
                   └─────────┘

Benefits:
✅ Decoupling: Producer/Consumer independent
✅ Reliability: Messages persisted in queue
✅ Scalability: Multiple consumers
✅ Load Leveling: Handle traffic spikes
```

### **Pub-Sub Pattern** 📢
```
                   ┌─────────────┐
              ┌───▶│ SUBSCRIBER 1│
              │    │  (Email)    │
              │    └─────────────┘
┌──────────┐  │    ┌─────────────┐
│PUBLISHER │──┼───▶│ SUBSCRIBER 2│
│(Order Svc│  │    │   (SMS)     │
└──────────┘  │    └─────────────┘
              │    ┌─────────────┐
              └───▶│ SUBSCRIBER 3│
                   │ (Analytics) │
                   └─────────────┘

Example: Order Placed Event
1. Order Service publishes "OrderPlaced"
2. Email Service sends confirmation
3. SMS Service sends delivery updates  
4. Analytics Service tracks metrics
```

## 🌐 CDN (Content Delivery Network)

### **Global CDN Distribution** 🗺️
```
                    ORIGIN SERVER
                    (US-East)
                         │
            ┌────────────┼────────────┐
            │            │            │
            ▼            ▼            ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │ CDN Europe  │ │ CDN US-West │ │ CDN Asia    │
    │ (London)    │ │ (California)│ │ (Singapore) │
    └─────────────┘ └─────────────┘ └─────────────┘
            │            │            │
            ▼            ▼            ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │European     │ │US West      │ │Asian        │
    │Users        │ │Users        │ │Users        │
    └─────────────┘ └─────────────┘ └─────────────┘

Latency Comparison:
Without CDN: London user → US server = 150ms
With CDN: London user → London CDN = 20ms
Improvement: 87% reduction in latency
```

### **CDN Cache Strategy** 📦
```
USER REQUEST → CDN Edge Server
                      │
               ┌──────┼──────┐
               ▼      │      ▼
        ┌─────────┐   │  ┌─────────┐
        │CACHE HIT│   │  │CACHE    │
        │Return   │   │  │MISS     │
        │Content  │   │  └─────────┘
        └─────────┘   │       │
                      │       ▼
                      │  Fetch from Origin
                      │       │
                      │       ▼
                      │  Cache Content
                      │       │
                      │       ▼
                      │  Return to User
                      │
               Response Time:
               Cache Hit: 20ms
               Cache Miss: 200ms
```

## 🔐 Security Patterns

### **Authentication vs Authorization** 🔑
```
                    CLIENT LOGIN
                         │
                         ▼
              ┌─────────────────────┐
              │   AUTHENTICATION    │
              │  "Who are you?"     │
              │                     │
              │ Username/Password   │
              │ → JWT Token         │
              └─────────────────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │   AUTHORIZATION     │
              │ "What can you do?"  │
              │                     │
              │ JWT Token →         │
              │ Check Permissions   │
              └─────────────────────┘
                         │
                         ▼
                  ACCESS GRANTED/DENIED

Example:
Authentication: John Doe (verified user)
Authorization: Can read posts, cannot delete posts
```

### **API Gateway Security** 🛡️
```
    CLIENT
       │
       ▼
┌─────────────┐
│ API GATEWAY │
├─────────────┤
│Rate Limiting│ ← 100 requests/minute per user
│             │
│Auth Check   │ ← Validate JWT token
│             │
│Input Valid  │ ← Sanitize malicious input
│             │
│Load Balance │ ← Route to healthy services
└─────────────┘
       │
       ▼
┌─────────────┐
│ MICROSERVICE│
└─────────────┘

Security Layers:
1. DDoS Protection
2. Rate Limiting  
3. Authentication
4. Input Validation
5. SSL/TLS Encryption
```

## 📊 Monitoring and Observability

### **The Three Pillars** 📈
```
┌─────────────────────────────────────────────────────────┐
│                    OBSERVABILITY                        │
├─────────────────┬─────────────────┬─────────────────────┤
│     METRICS     │      LOGS       │      TRACES         │
│                 │                 │                     │
│ ┌─────────────┐ │ ┌─────────────┐ │ ┌─────────────────┐ │
│ │CPU: 80%     │ │ │ERROR: DB    │ │ │Request Journey: │ │
│ │Memory: 60%  │ │ │connection   │ │ │                 │ │
│ │Requests/sec │ │ │failed       │ │ │API Gateway      │ │
│ │Error rate   │ │ │             │ │ │    ↓            │ │
│ └─────────────┘ │ │Timestamp    │ │ │User Service     │ │
│                 │ │Level: ERROR │ │ │    ↓            │ │
│What happened?   │ │Message      │ │ │Database Query   │ │
│                 │ │             │ │ │    ↓            │ │
│                 │ │Why it       │ │ │Response         │ │
│                 │ │happened?    │ │ │                 │ │
│                 │ │             │ │ │How requests     │ │
│                 │ │             │ │ │flow through?    │ │
│                 │ └─────────────┘ │ └─────────────────┘ │
└─────────────────┴─────────────────┴─────────────────────┘
```

### **Health Check Pattern** 💊
```
                LOAD BALANCER
                      │
           ┌──────────┼──────────┐
           │          │          │
           ▼          ▼          ▼
    ┌─────────┐ ┌─────────┐ ┌─────────┐
    │SERVER 1 │ │SERVER 2 │ │SERVER 3 │
    │ Status: │ │ Status: │ │ Status: │
    │ HEALTHY │ │ HEALTHY │ │  SICK   │
    └─────────┘ └─────────┘ └─────────┘
           ▲          ▲          ▲
           │          │          │
    ┌──────┴──────────┴──────────┴──────┐
    │     HEALTH CHECK SERVICE          │
    │                                   │
    │ /health endpoints every 30 sec    │
    │                                   │
    │ Server 1: ✅ 200 OK               │
    │ Server 2: ✅ 200 OK               │  
    │ Server 3: ❌ 500 Error            │
    └───────────────────────────────────┘

Traffic Routing:
✅ Healthy servers: Receive traffic
❌ Unhealthy servers: Removed from rotation
```

## 🎯 System Design Interview Process

### **The Standard Approach** 📝
```
┌─────────────────────────────────────────────────────────┐
│                 DESIGN INTERVIEW FLOW                   │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ 1. CLARIFY REQUIREMENTS (5-10 min)                     │
│    ┌─────────────────────────────────────────────────┐ │
│    │ • Functional: What should the system do?       │ │
│    │ • Non-functional: Scale, performance, etc.     │ │
│    │ • Constraints: Budget, time, team size         │ │
│    └─────────────────────────────────────────────────┘ │
│                           │                             │
│                           ▼                             │
│ 2. ESTIMATE SCALE (5 min)                              │
│    ┌─────────────────────────────────────────────────┐ │
│    │ • Users, requests/sec, storage needs           │ │
│    │ • Read vs write ratio                          │ │
│    │ • Peak vs average load                         │ │
│    └─────────────────────────────────────────────────┘ │
│                           │                             │
│                           ▼                             │
│ 3. HIGH-LEVEL DESIGN (10-15 min)                       │
│    ┌─────────────────────────────────────────────────┐ │
│    │ User → Load Balancer → Servers → Database       │ │
│    │ Draw major components and connections           │ │
│    │ Identify data flow                              │ │
│    └─────────────────────────────────────────────────┘ │
│                           │                             │
│                           ▼                             │
│ 4. DETAILED DESIGN (15-20 min)                         │
│    ┌─────────────────────────────────────────────────┐ │
│    │ • Database schema design                        │ │
│    │ • API design                                    │ │
│    │ • Algorithm details                             │ │
│    │ • Component interactions                        │ │
│    └─────────────────────────────────────────────────┘ │
│                           │                             │
│                           ▼                             │
│ 5. SCALE THE DESIGN (10-15 min)                        │
│    ┌─────────────────────────────────────────────────┐ │
│    │ • Identify bottlenecks                          │ │
│    │ • Add caching, load balancing                   │ │
│    │ • Database scaling (sharding, replicas)        │ │
│    │ • Handle edge cases and failures                │ │
│    └─────────────────────────────────────────────────┘ │
│                           │                             │
│                           ▼                             │
│ 6. MONITORING & WRAP-UP (5 min)                        │
│    ┌─────────────────────────────────────────────────┐ │
│    │ • Key metrics to monitor                        │ │
│    │ • Trade-offs made                               │ │
│    │ • Next steps for improvement                    │ │
│    └─────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

### **Common Mistakes to Avoid** ❌
```
🚫 DIVING INTO DETAILS TOO EARLY
   ├─ Start broad, then narrow down
   └─ Get requirements clear first

🚫 IGNORING NON-FUNCTIONAL REQUIREMENTS  
   ├─ Ask about scale, latency, consistency
   └─ These drive architectural decisions

🚫 NOT THINKING ABOUT FAILURES
   ├─ What happens when servers crash?
   └─ How do you handle network partitions?

🚫 OVER-ENGINEERING FOR DAY ONE
   ├─ Start simple, then scale
   └─ Don't build for 1 billion users immediately

🚫 FORGETTING ABOUT TRADE-OFFS
   ├─ Every decision has pros and cons
   └─ Explain why you chose approach A over B
```

---

*"The best system designs are not the most complex ones - they're the ones that solve the problem with the right level of complexity for the given constraints."*