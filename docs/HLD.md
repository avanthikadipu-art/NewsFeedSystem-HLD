# News Feed System - High Level Design

**Author:** Avanthika

**Version:** 1.0

---

# 1. Introduction

## 1.1 Overview

The News Feed System is a scalable social networking platform that allows users to create posts, follow other users, and receive a personalized news feed. The system is designed to handle millions of active users while providing fast response times and high availability.

Users can upload text, images, and videos, interact with posts through likes, comments, and shares, and discover new content using search and trending features.

The architecture follows a microservices-based design to improve scalability, maintainability, and fault tolerance.

---

## 1.2 Objectives

The objectives of the system are:

- Build a scalable social media platform.
- Generate personalized news feeds.
- Support real-time user engagement.
- Handle millions of users efficiently.
- Ensure high availability and reliability.
- Reduce feed generation latency.
- Support future feature expansion.
# 2. Functional Requirements

The system provides the following features.

## User Management

- User Registration
- User Login
- User Authentication
- User Profile
- Follow Users
- Unfollow Users

## Content Management

- Create Posts
- Edit Posts
- Delete Posts
- Upload Images
- Upload Videos

## News Feed

- Personalized Feed
- Reverse Chronological Feed
- Infinite Scrolling
- Feed Refresh

## Engagement

- Like Posts
- Unlike Posts
- Comment on Posts
- Share Posts

## Search

- Search Users
- Search Posts
- Trending Posts
# 3. Non Functional Requirements

| Requirement | Description |
|-------------|-------------|
| Scalability | Support millions of users and billions of posts. |
| Availability | Achieve 99.99% uptime with no single point of failure. |
| Performance | Generate feeds within 500 milliseconds. |
| Reliability | Ensure no data loss and durable storage. |
| Consistency | Strong consistency for user actions and eventual consistency for feed generation. |
| Security | Secure authentication and encrypted communication. |
| Extensibility | Easily add new services and features. |
# 4. Capacity Estimation

## Assumptions

To estimate the system capacity, the following assumptions are made:

- Total Registered Users: 100 Million
- Daily Active Users (DAU): 10 Million
- Average Posts per User per Day: 2
- Average Feed Requests per User per Day: 20
- Average Post Size: 2 KB (excluding media)
- Average Image Size: 500 KB
- Average Video Size: 10 MB

## Traffic Estimation

### Post Creation

Daily Posts = 10 Million × 2

= **20 Million Posts per Day**

Posts per Second (PPS)

20,000,000 / 86,400

≈ **231 Posts/Second**

---

### Feed Requests

Daily Feed Requests

10 Million × 20

= **200 Million Feed Requests/Day**

Requests per Second (RPS)

200,000,000 / 86,400

≈ **2,315 Requests/Second**

---

### Storage Estimation

Daily Storage for Text Posts

20 Million × 2 KB

≈ **40 GB/day**

Yearly Storage

40 × 365

≈ **14.6 TB/year**

Media files are stored separately in Object Storage (Amazon S3 or similar) to reduce database size.

---

### Network Bandwidth

The system is read-heavy because feed requests are much more frequent than post creation.

Read : Write Ratio ≈ 10 : 1

Therefore, caching plays a major role in improving performance.
# 5. System Architecture

## Architecture Style

The News Feed System follows a **Microservices Architecture**, where each business functionality is implemented as an independent service. This allows each service to scale independently, improves fault isolation, and makes the system easier to maintain.

The architecture consists of the following layers:

- Client Layer
- API Gateway
- Microservices Layer
- Messaging Layer
- Cache Layer
- Database Layer
- Media Storage
- Content Delivery Network (CDN)

---

## Components

### Client Layer

The client layer consists of web browsers and mobile applications. Users interact with the system to register, log in, create posts, view feeds, like posts, comment, share content, and search for users or posts.

---

### Load Balancer

The Load Balancer distributes incoming traffic across multiple application servers.

Responsibilities:

- Prevent server overload
- Improve availability
- Increase throughput
- Route requests to healthy servers

---

### API Gateway

The API Gateway acts as the single entry point for all client requests.

Responsibilities:

- Authentication
- Request Routing
- Rate Limiting
- Logging
- SSL Termination

---

### User Service

Responsible for:

- User Registration
- Login
- Profile Management
- Follow / Unfollow Users

---

### Post Service

Responsible for:

- Create Posts
- Edit Posts
- Delete Posts
- Retrieve Posts

---

### Feed Service

Responsible for:

- Personalized Feed Generation
- Feed Ranking
- Pagination
- Infinite Scrolling

---

### Engagement Service

Responsible for:

- Likes
- Comments
- Shares
- Engagement Count

---

### Search Service

Responsible for:

- Search Users
- Search Posts
- Trending Topics

---

### Notification Service

Responsible for notifying users about:

- Likes
- Comments
- New Followers
- Shares

---

### Media Service

Responsible for:

- Image Upload
- Video Upload
- Compression
- Storage

---

### Redis Cache

Redis stores frequently accessed data such as:

- User Profiles
- Personalized Feeds
- Trending Posts
- Popular Posts

Caching significantly reduces database load.

---

### Kafka

Kafka is used as the message broker for asynchronous communication between services.

Example Events:

- Post Created
- User Followed
- Like Added
- Comment Added

---

### Database Layer

The system uses multiple databases:

- PostgreSQL → User and Authentication Data
- Cassandra → Posts and Feed Data
- Redis → Cache

---

### Object Storage

Images and videos are stored in Object Storage (Amazon S3 or equivalent). The database stores only the media URLs.

---

### CDN

A Content Delivery Network (CDN) delivers media content from servers closest to users, reducing latency and improving user experience.
# 6. Feed Generation Strategy

The News Feed System is responsible for displaying a personalized timeline for every user. Since the platform supports millions of users, selecting the right feed generation strategy is critical.

## Fan-out-on-Write (Push Model)

Whenever a user creates a new post, the system immediately pushes that post into the news feeds of all followers.

### Advantages

- Fast feed loading
- Low read latency
- Better user experience

### Disadvantages

- Expensive write operations
- High storage usage
- Not suitable for celebrity accounts

---

## Fan-out-on-Read (Pull Model)

Posts are stored only once. When a user opens their feed, the system retrieves posts from followed users and generates the feed dynamically.

### Advantages

- Low storage usage
- Efficient for celebrity accounts
- Simple write operations

### Disadvantages

- Higher read latency
- Increased database load

---

## Hybrid Approach (Chosen Strategy)

The proposed system adopts a Hybrid Feed Generation Strategy.

- Normal users use Fan-out-on-Write.
- Celebrity users use Fan-out-on-Read.

This approach balances write efficiency and read performance while supporting millions of users.

### Benefits

- Better scalability
- Reduced storage overhead
- Faster feed loading for normal users
- Efficient handling of celebrity accounts
# 7. Database Design

## Database Selection

The system uses a polyglot persistence approach, where different databases are selected based on their strengths.

| Database | Purpose |
|----------|---------|
| PostgreSQL | User accounts, authentication, follow relationships |
| Cassandra | Posts, news feed, activity data |
| Redis | Caching frequently accessed data |
| Amazon S3 | Images and videos |

---

## Entity Design

### User

Attributes:

- User ID
- Username
- Email
- Password Hash
- Profile Picture
- Bio
- Created At

---

### Post

Attributes:

- Post ID
- User ID
- Content
- Media URL
- Post Type
- Timestamp
- Visibility

---

### Follow

Attributes:

- Follower ID
- Following ID
- Follow Date

---

### Like

Attributes:

- Like ID
- User ID
- Post ID
- Timestamp

---

### Comment

Attributes:

- Comment ID
- Post ID
- User ID
- Comment Text
- Timestamp

---

### Share

Attributes:

- Share ID
- User ID
- Post ID
- Timestamp

---

### Feed

Attributes:

- Feed ID
- User ID
- Post ID
- Ranking Score
- Timestamp

---

## Sharding Strategy

Posts are partitioned using User ID to distribute data evenly across database nodes.

Followers are partitioned based on User ID.

This ensures balanced storage and faster query execution.

---

## Replication

Each database node maintains multiple replicas.

Benefits include:

- High Availability
- Fault Tolerance
- Disaster Recovery
# 8. API Design

The system exposes RESTful APIs for communication between clients and backend services.

## User APIs

| Method | Endpoint | Description |
|---------|----------|-------------|
| POST | /register | Register User |
| POST | /login | User Login |
| GET | /users/{id} | View Profile |
| POST | /follow/{id} | Follow User |
| DELETE | /follow/{id} | Unfollow User |

---

## Post APIs

| Method | Endpoint | Description |
|---------|----------|-------------|
| POST | /posts | Create Post |
| GET | /posts/{id} | Get Post |
| PUT | /posts/{id} | Update Post |
| DELETE | /posts/{id} | Delete Post |

---

## Feed APIs

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | /feed | Get Personalized Feed |
| GET | /feed/refresh | Refresh Feed |

---

## Engagement APIs

| Method | Endpoint | Description |
|---------|----------|-------------|
| POST | /like/{id} | Like Post |
| DELETE | /like/{id} | Unlike Post |
| POST | /comment/{id} | Add Comment |
| POST | /share/{id} | Share Post |

---

## Search APIs

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | /search/users | Search Users |
| GET | /search/posts | Search Posts |
| GET | /trending | Trending Posts |
# 9. Data Flow

## Post Creation Flow

1. User creates a post.
2. Request reaches API Gateway.
3. API Gateway forwards the request to the Post Service.
4. Post Service stores the post in Cassandra.
5. Media files are uploaded to Object Storage.
6. Kafka publishes a "Post Created" event.
7. Feed Service updates followers' feeds.
8. Redis cache is updated.

---

## Feed Retrieval Flow

1. User opens the application.
2. Request reaches API Gateway.
3. Feed Service checks Redis Cache.
4. If cache exists, feed is returned immediately.
5. Otherwise, Feed Service retrieves posts from Cassandra.
6. Feed is cached in Redis.
7. Feed is returned to the user.

---

## Like Flow

1. User likes a post.
2. Engagement Service updates the database.
3. Kafka publishes a Like event.
4. Notification Service notifies the post owner.
5. Cache is updated.
# 10. Design Decisions and Trade-offs

## Design Decisions

- Microservices architecture for independent scalability.
- Redis is used to reduce database load.
- Kafka enables asynchronous communication.
- Cassandra stores high-volume post data.
- PostgreSQL manages relational user data.
- CDN improves media delivery performance.
- Hybrid Feed Generation balances read and write efficiency.

---

## Trade-offs

| Decision | Benefit | Limitation |
|----------|----------|------------|
| Microservices | Independent scaling | Increased operational complexity |
| Redis Cache | Faster response time | Cache invalidation required |
| Cassandra | High write throughput | Eventual consistency |
| Kafka | Loose coupling | Additional infrastructure |
| Hybrid Feed | Better scalability | More complex implementation |
# 11. Conclusion

The proposed News Feed System is designed to support millions of users while maintaining high availability, scalability, and performance. The system uses a microservices architecture, Redis caching, Kafka messaging, PostgreSQL for relational data, and Cassandra for high-volume post storage.

A Hybrid Feed Generation strategy ensures efficient handling of both regular users and celebrity accounts. The architecture is fault tolerant, extensible, and capable of supporting future enhancements such as AI-based recommendations, live streaming, and advanced analytics.

This design satisfies the functional and non-functional requirements of a modern large-scale social media platform.