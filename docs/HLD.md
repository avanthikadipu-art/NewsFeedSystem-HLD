# High Level Design
# 4. Capacity Estimation

Capacity estimation helps in designing a system that can efficiently support millions of users while maintaining low latency and high availability.

## Assumptions

| Parameter | Value |
|-----------|-------|
| Total Registered Users | 100 Million |
| Daily Active Users (DAU) | 10 Million |
| Average Posts per User per Day | 2 |
| Average Feed Requests per User per Day | 20 |
| Average Likes per User per Day | 10 |
| Average Comments per User per Day | 3 |
| Average Post Size | 2 KB |
| Average Image Size | 500 KB |

---

## Write Traffic

### Posts Created Per Day

10 Million Users × 2 Posts

= **20 Million Posts/Day**

Posts Per Second

20,000,000 / 86,400 ≈ **231 Posts/Second**

---

## Read Traffic

Feed Requests

10 Million Users × 20 Requests

= **200 Million Feed Requests/Day**

Feed Requests Per Second

200,000,000 / 86,400 ≈ **2315 Requests/Second**

This shows that the News Feed System is **read-heavy**, meaning significantly more read operations occur than write operations.

---

## Storage Estimation

### Text Posts

20 Million × 2 KB

≈ 40 GB/day

≈ 14.6 TB/year

### Images

20 Million × 500 KB

≈ 10 TB/day

≈ 3.65 PB/year

Therefore, media files should be stored in object storage rather than the primary database.

---

## Bandwidth Estimation

Assuming each feed contains 20 posts:

20 × 2 KB = 40 KB

For 2315 feed requests/second:

2315 × 40 KB ≈ 90 MB/sec

A CDN is recommended for delivering media content efficiently and reducing server load.

---

## Design Implications

Based on the above estimation:

- The system is read-intensive.
- Feed generation must be optimized.
- Redis cache should be used for frequently accessed feeds.
- Object storage should be used for media files.
- Horizontal scaling should be supported for all stateless services.