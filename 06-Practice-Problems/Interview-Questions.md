# 🏋️ System Design Practice Problems

*"Practice doesn't make perfect. Perfect practice makes perfect."*

## 🌱 Beginner Level Problems

### **Problem 1: Design a URL Shortener (like bit.ly)** 🔗

**Requirements Gathering:**
```
Functional Requirements:
✅ Shorten long URLs to short URLs
✅ Redirect short URLs to original long URLs  
✅ Custom aliases (optional)
✅ Expiration time for URLs
✅ Analytics (click count, location, etc.)

Non-Functional Requirements:
📊 100M URLs shortened per month
📊 100:1 read to write ratio
📊 URL redirection should be fast (< 100ms)
📊 Service should be highly available
📊 URLs should not be guessable
```

**Practice Steps:**
```
Step 1: Capacity Estimation
- URLs created per month: 100M
- URLs created per second: 100M / (30 * 24 * 3600) = ~40 URLs/sec
- Read QPS: 40 * 100 = 4000 URLs/sec
- Storage for 5 years: 100M * 12 * 5 = 6B URLs
- Storage per URL: ~500 bytes (URL + metadata)
- Total storage: 6B * 500 bytes = 3TB

Step 2: System Design
- Create high-level architecture diagram
- Design database schema
- Choose encoding strategy (Base62)
- Add caching layer for popular URLs

Step 3: Deep Dive
- Handle custom aliases
- Design analytics system
- Plan for scaling
- Consider security aspects
```

**Expected Solution Components:**
- Load balancer
- Web servers  
- Database (MySQL/PostgreSQL)
- Cache (Redis)
- Analytics service

**Common Mistakes:**
❌ Not considering URL collision handling
❌ Forgetting about analytics requirements
❌ Not planning for cache invalidation

---

### **Problem 2: Design a Pastebin (like pastebin.com)** 📝

**Requirements:**
```
Functional:
✅ Users can paste text and get a unique URL
✅ Users can access text via the URL
✅ Support for expiration time
✅ Support for private/public pastes
✅ Syntax highlighting (optional)

Non-Functional:
📊 1M new pastes per month  
📊 5:1 read to write ratio
📊 Text size limit: 10MB per paste
📊 Data retention: 1 year default
```

**Key Focus Areas:**
- Text storage strategy
- Database vs file system
- Caching frequently accessed pastes
- Handling large text files

---

### **Problem 3: Design a Chat Application** 💬

**Requirements:**
```
Functional:
✅ Send/receive messages in real-time
✅ Group chats (up to 100 users)
✅ Online/offline status
✅ Message history
✅ File sharing (images, documents)

Non-Functional:
📊 1M daily active users
📊 Each user sends 40 messages per day
📊 Message delivery: < 100ms
📊 Support for mobile and web clients
```

**Key Focus Areas:**
- Real-time communication (WebSockets)
- Message ordering and delivery
- Presence system
- Mobile optimization

---

## 🚀 Intermediate Level Problems

### **Problem 4: Design Twitter** 🐦

**Requirements:**
```
Functional:
✅ Post tweets (280 characters)
✅ Follow/unfollow users
✅ Timeline (home and user timeline)
✅ Like and retweet
✅ Search tweets
✅ Trending topics

Non-Functional:
📊 300M monthly active users
📊 200M daily active users  
📊 Users post 2 tweets per day on average
📊 Timeline should load in < 200ms
📊 100:1 read to write ratio
```

**Advanced Considerations:**
- Timeline generation strategies (push vs pull)
- Celebrity user problem
- Tweet fanout mechanisms
- Real-time search indexing
- Trending algorithm design

**Practice Approach:**
```
1. Start with basic tweet posting and retrieval
2. Add following relationships
3. Design timeline generation (focus on this!)
4. Add caching layers
5. Scale for celebrity users
6. Add search functionality
7. Design trending topics
```

---

### **Problem 5: Design Instagram** 📸

**Requirements:**
```
Functional:
✅ Upload/view photos and videos
✅ Follow users and see their feed
✅ Like and comment on posts
✅ Search by hashtags and users
✅ Stories (24-hour posts)

Non-Functional:
📊 500M daily active users
📊 2M new photos uploaded daily
📊 Average photo size: 200KB
📊 Feed should load in < 200ms
📊 Support mobile-first design
```

**Key Challenges:**
- Image storage and CDN strategy
- Feed generation for media-heavy content
- Image processing pipeline
- Mobile app optimization
- Metadata vs media storage separation

---

### **Problem 6: Design WhatsApp** 📱

**Requirements:**
```
Functional:
✅ Send/receive text messages
✅ Group chats (up to 256 members)  
✅ Media sharing (photos, videos, documents)
✅ Voice and video calls
✅ End-to-end encryption
✅ Message delivery status (sent, delivered, read)

Non-Functional:
📊 2B registered users
📊 1B daily active users
📊 60B messages per day
📊 Message delivery: < 100ms
📊 99.99% availability
```

**Complex Aspects:**
- Message delivery guarantees
- End-to-end encryption design
- Group message handling
- Offline message storage
- Voice/video call infrastructure

---

## 🏆 Advanced Level Problems

### **Problem 7: Design Netflix** 🎬

**Requirements:**
```
Functional:
✅ Stream videos to users globally
✅ Browse and search content catalog
✅ User profiles and recommendations
✅ Download for offline viewing
✅ Multiple device support
✅ Content upload for Netflix Originals

Non-Functional:
📊 200M+ subscribers globally
📊 1B hours watched daily
📊 Video start time: < 3 seconds
📊 Support 4K streaming
📊 Global content distribution
```

**Technical Challenges:**
- Video encoding and adaptive bitrate streaming
- Global CDN design
- Recommendation system architecture
- Content metadata management
- Multi-device synchronization

---

### **Problem 8: Design Uber** 🚗

**Requirements:**
```
Functional:
✅ Request rides
✅ Match riders with drivers
✅ Real-time location tracking
✅ Trip management and payment
✅ Rating system
✅ Surge pricing

Non-Functional:
📊 100M rides per month
📊 1M drivers active
📊 Sub-second matching time
📊 Real-time location updates
📊 Global availability
```

**Unique Challenges:**
- Geo-spatial data handling
- Real-time matching algorithm
- Location tracking at scale
- Dynamic pricing algorithms
- Handling high concurrency during peak hours

---

## 📝 Practice Templates

### **Problem-Solving Template** 📋
```
□ STEP 1: REQUIREMENTS (10 minutes)
  Functional Requirements:
  - [ ] Requirement 1
  - [ ] Requirement 2
  - [ ] Requirement 3
  
  Non-Functional Requirements:
  - [ ] Scale: ___ users, ___ requests/sec
  - [ ] Performance: ___ ms response time
  - [ ] Availability: ___% uptime
  - [ ] Consistency: Strong/Eventual

□ STEP 2: CAPACITY ESTIMATION (5 minutes)
  - [ ] Storage: ___ TB
  - [ ] Bandwidth: ___ Gbps
  - [ ] Memory: ___ GB
  - [ ] QPS: ___ queries/sec

□ STEP 3: HIGH-LEVEL DESIGN (15 minutes)
  - [ ] Draw major components
  - [ ] Show data flow
  - [ ] Identify services
  - [ ] Add load balancer

□ STEP 4: DETAILED DESIGN (20 minutes)
  - [ ] Database schema
  - [ ] API design
  - [ ] Algorithm details
  - [ ] Component interactions

□ STEP 5: SCALE THE DESIGN (15 minutes)
  - [ ] Identify bottlenecks
  - [ ] Add caching
  - [ ] Database scaling
  - [ ] Handle failures
```

### **Self-Evaluation Rubric** 📊
```
REQUIREMENTS GATHERING (0-5 points)
□ 5: Asked comprehensive functional and non-functional questions
□ 4: Asked good functional questions, some non-functional
□ 3: Asked basic functional questions
□ 2: Asked few questions, made assumptions
□ 1: Jumped into solution without asking questions

SYSTEM DESIGN (0-5 points)  
□ 5: Clean, scalable design with proper component separation
□ 4: Good design with minor issues
□ 3: Basic design that works but not optimized
□ 2: Design has significant issues
□ 1: Design doesn't meet requirements

SCALING APPROACH (0-5 points)
□ 5: Identified bottlenecks and provided multiple scaling solutions
□ 4: Good scaling approach with some solutions
□ 3: Basic scaling considerations
□ 2: Mentioned scaling but limited solutions
□ 1: No scaling considerations

COMMUNICATION (0-5 points)
□ 5: Clear explanations, engaged with feedback, discussed trade-offs
□ 4: Good communication with minor gaps
□ 3: Adequate communication
□ 2: Some communication issues
□ 1: Poor communication, didn't explain decisions

TARGET SCORE: 16+ (Good), 18+ (Great), 20 (Excellent)
```

## 🎯 Focused Practice Sessions

### **30-Minute Quick Practice** ⏰
```
Choose a simple problem (URL shortener, Pastebin)
- 5 min: Requirements and estimation
- 10 min: High-level design
- 10 min: Database schema and APIs
- 5 min: Basic scaling considerations

Focus: Speed and covering all basics
```

### **60-Minute Deep Dive** 🏊‍♂️
```
Choose medium complexity problem (Twitter, Instagram)
- 10 min: Thorough requirements gathering
- 10 min: Detailed capacity estimation
- 20 min: Complete system design
- 15 min: Scaling and advanced features
- 5 min: Monitoring and trade-offs

Focus: Depth and handling complex scenarios
```

### **90-Minute Interview Simulation** 🎭
```
Choose advanced problem (Netflix, Uber)
- Full interview experience with partner
- Include follow-up questions
- Practice explaining trade-offs
- Handle unexpected requirements
- Get feedback on communication

Focus: Interview readiness and pressure handling
```

## 📈 Progressive Difficulty Path

### **Week 1-2: Foundation Building** 🏗️
```
Day 1: URL Shortener (basic version)
Day 3: Pastebin (focus on storage)
Day 5: Chat App (basic real-time features)
Day 7: Review and compare all three designs

Focus: Get comfortable with basic patterns
```

### **Week 3-4: Adding Complexity** 🔧
```
Day 8: Social Media Feed (timeline generation)
Day 10: Photo Sharing App (media handling)
Day 12: Notification System (push notifications)
Day 14: Review scaling patterns across systems

Focus: Handle more complex data flows
```

### **Week 5-6: Real-World Scale** 🌍
```
Day 15: Twitter (complete system)
Day 17: WhatsApp (messaging at scale)
Day 19: Video Streaming (Netflix basics)
Day 21: Compare designs and identify patterns

Focus: Handle realistic scale and constraints
```

### **Week 7-8: Advanced Systems** 🚀
```
Day 22: Search Engine (distributed indexing)  
Day 24: E-commerce Platform (complex transactions)
Day 26: Ride Sharing (real-time geo systems)
Day 28: Final review and mock interviews

Focus: Complex algorithms and edge cases
```

## 🤝 Study Group Activities

### **Design Review Sessions** 👥
```
Activity: Peer design reviews
- Present your design (15 min)
- Get feedback from peers (10 min)  
- Discuss alternative approaches (10 min)
- Document lessons learned (5 min)

Benefits: Multiple perspectives, communication practice
```

### **Mock Interview Rounds** 🎪
```
Activity: Role-play interviews
- One person as interviewer, one as candidate
- 45-minute full interview simulation
- Switch roles and try different problem
- Group discussion on improvements

Benefits: Interview pressure simulation, feedback
```

### **Architecture Debates** 🗣️
```
Activity: Defend design decisions
- Present two different solutions to same problem
- Debate pros/cons of each approach
- Vote on preferred solution with reasoning
- Document winning arguments

Benefits: Critical thinking, trade-off analysis
```

---

*"The goal is not to memorize solutions, but to develop a systematic approach to breaking down complex problems into manageable components."*