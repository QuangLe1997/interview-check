# Common System Design Interview Scenarios - Detailed Guide

## 1. Design a Social Media Feed System

### Requirements Analysis
- **Functional Requirements**
  - Users can create posts with text/images/videos
  - Users can view their personalized feed
  - Users can like and comment on posts
  - Posts appear in followers' feeds
  - Support for pagination
  
- **Non-Functional Requirements**
  - Feed loading time < 2 seconds
  - Support millions of active users
  - High availability (99.99%)
  - Eventually consistent model acceptable
  - Global accessibility

### System Components
1. **Data Storage Layer**
   ```
   User Table:
   - user_id (PK)
   - username
   - profile_info
   - created_at
   
   Post Table:
   - post_id (PK)
   - user_id (FK)
   - content
   - media_urls
   - created_at
   
   Follow Table:
   - follower_id
   - followee_id
   - created_at
   Composite Key: (follower_id, followee_id)
   ```

2. **Feed Generation Service**
   - Pull model: Generate feed on request
   - Push model: Pre-generate feeds
   - Hybrid approach: Fan-out on write for active users

3. **Caching Strategy**
   ```
   Cache Layers:
   - Recent posts cache (Redis)
   - User feed cache (Redis)
   - Content CDN (for media)
   
   Cache Policy:
   - LRU eviction
   - 24-hour TTL for feeds
   - 7-day TTL for posts
   ```

### Scale Considerations
- **Read-Heavy System**
  - Read:Write ratio ≈ 100:1
  - Implement read replicas
  - Heavy caching
  
- **Data Partitioning**
  - Shard by user_id
  - Separate hot and cold data
  - Archive old posts

## 2. Design a Real-Time Chat System

### Core Requirements
- **Functional**
  - 1-on-1 messaging
  - Group chats (up to 100 members)
  - Online/offline status
  - Message history
  - Read receipts
  
- **Non-Functional**
  - Message delivery < 100ms
  - Offline message support
  - End-to-end encryption
  - Support for millions of concurrent connections

### Technical Architecture
1. **Connection Management**
   ```
   WebSocket Server:
   - Connection pooling
   - Heart-beat mechanism
   - Connection restoration
   
   State Management:
   - User sessions
   - Presence information
   - Connection metadata
   ```

2. **Message Flow**
   ```
   Sending Message:
   1. Client → WebSocket Server
   2. Server validates message
   3. Server stores message
   4. Server pushes to recipient(s)
   5. Delivery confirmation
   
   Offline Handling:
   1. Store in offline queue
   2. Push notification
   3. Deliver on reconnect
   ```

3. **Storage Design**
   ```
   Message Store:
   - message_id
   - chat_id
   - sender_id
   - content
   - timestamp
   - status
   
   Chat Store:
   - chat_id
   - chat_type (1-1 or group)
   - participants
   - created_at
   ```

## 3. Design a URL Shortener

### System Requirements
- **Functional**
  - Shorten long URLs
  - Custom short URLs (optional)
  - URL redirection
  - URL analytics
  
- **Non-Functional**
  - 100ms redirection time
  - 99.9% availability
  - Unique short URLs
  - Scalable storage

### Technical Implementation
1. **URL Shortening Algorithm**
   ```python
   def generate_short_url(long_url):
       # MD5 hash of URL + timestamp
       unique_id = md5(long_url + timestamp)
       # Take first 6 characters
       short_code = base62_encode(unique_id[:6])
       return short_code
   ```

2. **Data Storage**
   ```
   URL Mapping:
   - short_url (PK)
   - long_url
   - user_id
   - created_at
   - expiry_date
   - click_count
   
   Analytics:
   - short_url (FK)
   - timestamp
   - referrer
   - user_agent
   - ip_address
   ```

3. **Caching Strategy**
   ```
   Cache Design:
   - Hot URLs in memory cache
   - LRU eviction policy
   - Write-through caching
   - Regional caching for faster access
   ```

## 4. Design a Notification System

### System Requirements
- **Functional**
  - Multiple notification types (email, push, SMS)
  - Template management
  - Delivery tracking
  - Rate limiting
  
- **Non-Functional**
  - < 5 minute delivery SLA
  - At-least-once delivery
  - Scalable to millions of notifications/day
  - Analytics and monitoring

### Architecture Components
1. **Notification Service**
   ```
   Components:
   - Template Engine
   - Rate Limiter
   - Priority Queue
   - Retry Mechanism
   
   Integration Points:
   - Email Service (SMTP)
   - Push Notification Service (FCM/APNS)
   - SMS Gateway
   ```

2. **Data Model**
   ```
   Notification:
   - notification_id
   - user_id
   - type
   - content
   - status
   - created_at
   - sent_at
   
   Template:
   - template_id
   - type
   - content
   - variables
   - version
   ```

3. **Processing Pipeline**
   ```
   Flow:
   1. Receive notification request
   2. Validate and enrich
   3. Template processing
   4. Rate limit check
   5. Queue for delivery
   6. Send to appropriate service
   7. Track delivery status
   ```

## Interview Tips

### Approaching Design Questions
1. **Start with Requirements**
   - Ask clarifying questions
   - Define scope
   - Establish constraints
   - Get scale requirements

2. **High-Level Design**
   - Draw main components
   - Show data flow
   - Identify interfaces
   - Discuss trade-offs

3. **Deep Dive**
   - Choose critical components
   - Explain technical decisions
   - Discuss alternatives
   - Address bottlenecks

### Common Follow-up Questions
1. **Scaling**
   - How to handle 10x traffic?
   - What breaks first?
   - Cost optimization?

2. **Reliability**
   - Failure handling?
   - Data consistency?
   - Backup strategy?

3. **Performance**
   - Latency optimization?
   - Cache effectiveness?
   - Resource utilization?

### Preparation Checklist
- [ ] Understand basic calculations (QPS, storage, etc.)
- [ ] Know common architectural patterns
- [ ] Practice drawing clear diagrams
- [ ] Prepare questions for interviewer
- [ ] Review recent system design trends
- [ ] Practice explaining technical decisions
