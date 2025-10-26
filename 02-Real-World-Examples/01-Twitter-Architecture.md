# 🐦 Case Study: Designing Twitter

*"How to build a system that handles 500 million tweets per day"*

## 🎓 Before We Start: Twitter in Simple Terms

### **What is Twitter, Really?** 🤔
```
Imagine a giant bulletin board where:
- Anyone can post short messages (tweets)
- People can "follow" others to see their messages
- Messages appear in your personal feed
- You can share others' messages (retweet) or like them

The challenge: How do you make this work for 500 MILLION people posting messages every day?
```

### **The Key Insight for Slow Learners** 💡
```
Twitter's main challenge isn't posting tweets (that's easy!)
Twitter's main challenge is: "How do we show the right tweets to each user instantly?"

Think about it:
- You follow 100 people
- Each person tweets 2 times per day
- That's 200 tweets per day you could see
- But Twitter has to figure this out for 200 million users
- And do it in less than 200 milliseconds!
```

### **Learning Approach for This Case Study** 📚
```
🌱 Beginner: Focus on the basic architecture and timeline generation
🌿 Intermediate: Understand the fanout strategies and caching
🌳 Advanced: Dive into celebrity user problems and global scaling

Don't try to understand everything at once!
Read through once for overall understanding, then come back for details.
```

### **Key Questions to Keep in Mind** ❓
```
As you read this case study, constantly ask yourself:
1. "Why did they choose this approach?"
2. "What would happen if they didn't do this?"
3. "How is this like the restaurant analogy I learned?"
4. "What would break if 10x more people used Twitter?"
```

## 📋 Requirements Gathering

### **Functional Requirements** ✅
```
✅ Users can post tweets (280 characters)
✅ Users can follow other users  
✅ Users can see timeline of tweets from people they follow
✅ Users can like and retweet
✅ Users can search tweets
✅ Users get notifications
```

### **Non-Functional Requirements** 📊
```
📊 Scale: 
   - 500M active users
   - 200M daily active users
   - 500M tweets per day
   - 100M timeline requests per day

⚡ Performance:
   - Timeline load: < 200ms
   - Tweet posting: < 100ms  
   - 99.9% availability

🔄 Read/Write Ratio: 100:1 (Read-heavy)
```

### **Back-of-Envelope Calculations** 🧮
```
Daily Active Users: 200M
Average tweets per user per day: 2.5
Total tweets per day: 500M tweets

Tweets per second: 500M / (24 * 3600) = ~6000 TPS
Peak load (3x average): 18,000 TPS

Storage per tweet: ~300 bytes (text + metadata)
Daily storage: 500M * 300 bytes = 150 GB/day
Annual storage: 150 GB * 365 = ~55 TB/year
```

## 🚶‍♂️ Step-by-Step Walkthrough (Follow Along!)

### **Step 1: What Happens When You Post a Tweet?** ✍️
```
1. You type "Hello World!" and hit Tweet
2. Your app sends this to Twitter's servers
3. Server saves your tweet in the database
4. Server needs to show this tweet to your followers
5. Challenge: You have 1 million followers - how do we notify them all?

This is where it gets interesting! Let's see how Twitter solves this...
```

### **Step 2: What Happens When You Open Twitter?** 📱
```
1. You open Twitter app
2. App asks: "Show me the timeline for this user"
3. Server thinks: "This user follows 200 people... let me get their recent tweets"
4. Server finds recent tweets from 200 people
5. Server sorts them by time and shows you the top 20
6. Challenge: This needs to happen in under 200ms!

The question is: How do we make step 3-4 super fast?
```

### **Step 3: The Big Problem - Timeline Generation** 🎯
```
Imagine you're running a newspaper:
- You have 200 million subscribers
- Each subscriber wants a personalized newspaper
- Each newspaper must be created in 0.2 seconds
- The news is constantly changing

How would you solve this? Keep thinking as we explore Twitter's solutions...
```

## 🏗️ High-Level Architecture

```
                    📱 Mobile Apps
                         |
                    🌐 Web Clients
                         |
    ┌─────────────────────┼─────────────────────┐
    |              Load Balancer                |
    └─────────────────────┼─────────────────────┘
                         |
         ┌──────────────────────────────────┐
         |        API Gateway               |
         └──────────────────────────────────┘
                         |
    ┌────────────────────────────────────────────┐
    |              Microservices                 |
    ├─────────────┬──────────────┬──────────────┤
    | User        | Tweet        | Timeline     |
    | Service     | Service      | Service      |
    └─────────────┴──────────────┴──────────────┘
                         |
    ┌────────────────────────────────────────────┐
    |               Data Layer                   |
    ├─────────────┬──────────────┬──────────────┤
    | User DB     | Tweet DB     | Timeline     |
    | (MySQL)     | (Cassandra)  | Cache (Redis)|
    └─────────────┴──────────────┴──────────────┘
```

## 🎯 Core Services Deep Dive

### **1. User Service** 👤
```
Responsibilities:
├── User registration/authentication
├── Profile management  
├── Follow/Unfollow relationships
└── User metadata

Database: MySQL (ACID compliance for user data)

Schema:
users: user_id, username, email, created_at
followers: follower_id, followed_id, created_at
```

### **2. Tweet Service** 🐦
```
Responsibilities:
├── Create tweets
├── Store tweet content
├── Handle likes/retweets
└── Tweet metadata management

Database: Cassandra (High write throughput)

Schema (Cassandra):
tweet_id | user_id | content | created_at | like_count
```

### **3. Timeline Service** 📰
```
Responsibilities:
├── Generate user timelines
├── Merge tweets from followed users
├── Cache popular timelines  
└── Real-time updates

Database: Redis (Fast timeline caching)
```

## 🔄 Timeline Generation Strategies

### **Strategy 1: Pull Model (Lazy Loading)** 🎣
```
User requests timeline →
1. Get list of followed users
2. Fetch recent tweets from each followed user  
3. Merge and sort by timestamp
4. Return top 20 tweets

Timeline Request Flow:
User → Timeline Service → Tweet Service → Database
```

**Pros**: 
- Low storage overhead
- Fresh data always

**Cons**:
- Slow timeline generation (multiple DB queries)
- High load on tweet service

### **Strategy 2: Push Model (Pre-computed)** 📤
```
User posts tweet →
1. Get follower list
2. Add tweet to each follower's timeline cache
3. Store in Redis for fast access

Timeline Request Flow:
User → Timeline Service → Redis Cache
```

**Pros**:
- Fast timeline reads
- Better user experience

**Cons**:
- High storage overhead
- Complex for users with millions of followers

### **Strategy 3: Hybrid Model** ⚖️ *(Twitter's Approach)*
```
Regular Users (< 1M followers):
└── Use Push Model (pre-compute timelines)

Celebrity Users (> 1M followers):  
└── Use Pull Model (compute on demand)

Timeline Generation:
├── Fetch pre-computed timeline (Push)
├── Add tweets from celebrities (Pull)
└── Merge and sort
```

## 🗄️ Database Design

### **User Database (MySQL)** 👥
```sql
-- Users table
CREATE TABLE users (
    user_id BIGINT PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,  
    display_name VARCHAR(100),
    bio TEXT,
    follower_count INT DEFAULT 0,
    following_count INT DEFAULT 0,
    verified BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW(),
    
    INDEX idx_username (username),
    INDEX idx_email (email)
);

-- Followers relationship
CREATE TABLE followers (
    follower_id BIGINT NOT NULL,
    followed_id BIGINT NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    
    PRIMARY KEY (follower_id, followed_id),
    INDEX idx_follower (follower_id),
    INDEX idx_followed (followed_id),
    
    FOREIGN KEY (follower_id) REFERENCES users(user_id),
    FOREIGN KEY (followed_id) REFERENCES users(user_id)
);
```

### **Tweet Database (Cassandra)** 🐦
```cql
-- Tweets table (partition by user_id for efficient user tweet lookups)
CREATE TABLE tweets (
    user_id BIGINT,
    tweet_id TIMEUUID,
    content TEXT,
    created_at TIMESTAMP,
    like_count INT,
    retweet_count INT,
    reply_to_tweet_id TIMEUUID,
    
    PRIMARY KEY (user_id, tweet_id)
) WITH CLUSTERING ORDER BY (tweet_id DESC);

-- Global tweet lookup (partition by tweet_id)
CREATE TABLE tweets_by_id (
    tweet_id TIMEUUID PRIMARY KEY,
    user_id BIGINT,
    content TEXT,
    created_at TIMESTAMP,
    like_count INT,
    retweet_count INT
);

-- Timeline table (for push model)
CREATE TABLE user_timeline (
    user_id BIGINT,
    tweet_id TIMEUUID,
    tweet_user_id BIGINT,
    created_at TIMESTAMP,
    
    PRIMARY KEY (user_id, tweet_id)
) WITH CLUSTERING ORDER BY (tweet_id DESC);
```

### **Cache Layer (Redis)** ⚡
```redis
# User timeline cache (list of tweet IDs)
user:timeline:123 → [tweet_id1, tweet_id2, tweet_id3, ...]

# Tweet content cache  
tweet:456 → {"content": "Hello world", "user_id": 123, "created_at": "..."}

# User profile cache
user:profile:123 → {"username": "john", "follower_count": 1500, ...}

# Hot tweets cache (trending)
trending:tweets → [tweet_id1, tweet_id2, ...]
```

## 🚀 Scaling Strategies

### **Database Scaling** 📊

**User Database Sharding**:
```
Shard by user_id:
├── Shard 1: user_id 1-100M
├── Shard 2: user_id 100M-200M  
└── Shard 3: user_id 200M-300M

Routing Logic:
shard_id = user_id % num_shards
```

**Tweet Database (Cassandra)**:
```
Natural sharding by partition key (user_id)
├── Partition 1: User tweets 1-1M
├── Partition 2: User tweets 1M-2M
└── Auto-distributed across cluster nodes
```

### **Caching Strategy** ⚡
```
Cache Hierarchy:
1. Browser Cache (Static assets)
2. CDN (Global tweet images/videos)  
3. Application Cache (Redis)
   ├── Hot user timelines
   ├── Popular tweets
   └── User profiles
4. Database Query Cache
```

### **Content Delivery** 🌍
```
Global CDN Strategy:
├── Static Assets (JS, CSS, Images)
├── Tweet Media (Photos, Videos)
└── Regional Timeline Caches

Geographic Distribution:
├── US East → Primary datacenter
├── US West → Read replicas
├── Europe → Read replicas + CDN
└── Asia → Read replicas + CDN
```

## 🔍 Advanced Features

### **Tweet Search** 🔎
```
Search Architecture:
Tweet → Kafka → Search Indexer → Elasticsearch

Search Flow:
User Query → Search Service → Elasticsearch → Results

Index Structure:
{
  "tweet_id": "123",
  "content": "System design is fun",
  "user_id": 456,
  "created_at": "2023-01-15",
  "hashtags": ["systemdesign", "learning"],
  "mentions": ["@username"]
}
```

### **Real-time Notifications** 🔔
```
Notification Flow:
1. User A likes User B's tweet
2. Event published to Kafka
3. Notification service consumes event
4. Push notification sent to User B

Architecture:
Tweet Service → Kafka → Notification Service → Push Service
                          ↓
                    WebSocket Connection → User B
```

### **Trending Topics** 📈
```
Trending Algorithm:
1. Count hashtag mentions in recent tweets (sliding window)
2. Apply decay function (recent tweets weighted more)
3. Compare to historical baseline
4. Filter spam/bot activity
5. Update trending list every 5 minutes

Data Pipeline:
Tweets → Stream Processing → Trending Calculator → Cache
```

## 🔄 Data Flow Examples

### **Posting a Tweet** ✍️
```
1. User submits tweet via mobile app
2. Load balancer routes to Tweet Service
3. Tweet Service validates (length, content)
4. Generate unique tweet_id (UUID)
5. Store tweet in Cassandra
6. Publish event to Kafka
7. Timeline Service receives event
8. For each follower:
   - Add tweet to follower's timeline cache (Redis)
9. Return success to user

Timeline: ~50ms total
```

### **Loading Timeline** 📱
```
1. User opens Twitter app
2. Request routed to Timeline Service  
3. Check Redis cache for user timeline
4. If cache hit: Return cached timeline
5. If cache miss:
   - Get followed users from User Service
   - Fetch recent tweets from Tweet Service
   - Merge and sort tweets
   - Cache result in Redis
6. Return timeline to user

Timeline: ~100ms (cache hit), ~800ms (cache miss)
```

## 📊 Monitoring & Metrics

### **Key Metrics** 📈
```
Business Metrics:
├── Daily Active Users (DAU)
├── Tweet posting rate  
├── Timeline engagement rate
└── User retention

Technical Metrics:
├── API response times
├── Database query performance
├── Cache hit rates
├── Error rates by service
└── System availability
```

### **Alerting Thresholds** 🚨
```
Critical Alerts:
├── API response time > 500ms
├── Error rate > 1%
├── Database connections > 80%
└── Cache hit rate < 90%

Warning Alerts:
├── Tweet posting rate unusual patterns
├── Timeline generation > 200ms
├── Memory usage > 70%
└── Disk space < 20%
```

## 🎯 Trade-offs & Decisions

### **Consistency vs Availability** 🤔
```
Decision: Choose Availability
Reasoning:
├── Social media can tolerate slight inconsistencies
├── Users prefer fast timelines over perfect consistency
├── Eventual consistency is acceptable for likes/retweets
└── Critical user data (login) uses strong consistency
```

### **Storage Costs** 💰
```
Tweet Retention Policy:
├── Hot tweets (< 7 days): SSD storage
├── Warm tweets (< 1 year): HDD storage  
├── Cold tweets (> 1 year): Archive storage
└── Media cleanup after 2 years (with user consent)
```

## 🏆 Lessons Learned

### **What Works Well** ✅
1. **Microservices**: Independent scaling and deployment
2. **Caching**: Dramatic performance improvements
3. **Async Processing**: Better user experience
4. **Read Replicas**: Handle read-heavy workload

### **Common Pitfalls** ❌
1. **Over-engineering**: Don't build for 1B users on day 1
2. **Cache Stampede**: Multiple processes rebuilding same cache
3. **Hot Shards**: Celebrities creating uneven load
4. **Cascade Failures**: One service failure affecting others

---

*"Twitter's architecture evolved over years. Start simple, measure everything, and scale incrementally based on real usage patterns."*