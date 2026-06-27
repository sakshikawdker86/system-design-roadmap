# 03 - Rate Limiter System Design

## Problem Statement

Design a Rate Limiter that prevents users from sending too many requests in a short period of time.

The goal is to protect backend services from abuse, brute-force attacks, and excessive traffic.

Example:

- Login API
- OTP API
- Public APIs
- Payment APIs

---

## Why do we need a Rate Limiter?

Without a Rate Limiter:

- A user can send unlimited requests.
- Attackers can perform brute-force attacks.
- Backend servers may become overloaded.
- Legitimate users may experience slow responses.

---

## Functional Requirements

- Allow requests within a defined limit.
- Reject requests after the limit is exceeded.
- Reset the limit after a specified time period.

---

## Non-Functional Requirements

- Fast request processing.
- Low latency.
- High availability.
- Should work correctly with multiple backend servers.
- Scalable for millions of users.

---

## Basic Architecture

Users
    │
    ▼
Load Balancer
    │
    ▼
Backend Servers
    │
    ▼
Redis (Request Counter)
    │
    ▼
Backend API

---

## Initial Design

Store the request count for each user.

Example:

User A → 5 Requests

User B → 8 Requests

If the limit is 10 requests per minute:

Request 11

↓

Reject Request

---

## Problem with Multiple Servers

Users
      │
Load Balancer
   /      \
Server1  Server2

If every server stores its own request count, users can bypass the limit because each server has different data.

---

## Solution

Store request counters in Redis.

All backend servers access the same request count.

Users
      │
Load Balancer
      │
Backend Servers
      │
Redis

---

## Why Redis?

- Very fast read operations.
- Very fast write operations.
- Supports automatic key expiration (TTL).
- Suitable for request counters.

---

## Fixed Window Algorithm

Rule:

10 Requests / Minute

Advantages

- Easy to implement.
- Low memory usage.

Disadvantages

- Users can exploit the boundary.

Example:

12:00:59 → 10 Requests

12:01:00 → 10 Requests

20 Requests are accepted within 2 seconds.

---

## Sliding Window Algorithm

Instead of checking fixed minute boundaries, always check the last 60 seconds.

Advantages

- Prevents boundary problems.
- More accurate request limiting.

Best For

- Login API
- OTP API
- Password Reset API

---

## Token Bucket Algorithm

Requests consume tokens.

Tokens are added continuously over time.

If tokens are available:

Request Allowed

Otherwise:

Request Rejected

Advantages

- Allows burst traffic.
- Smooth request handling.

Best For

- Image Upload
- File Upload
- API Gateway

---

## TTL (Time To Live)

Request counters should not stay forever.

Redis automatically removes expired counters after the configured time.

Example

User A

Count = 10

TTL = 60 seconds

After 60 seconds:

Counter is automatically removed.

---

## Key Learnings

- Rate Limiter protects backend services.
- Redis is commonly used because of fast reads, writes, and TTL support.
- Fixed Window is simple but has boundary issues.
- Sliding Window provides stricter rate limiting.
- Token Bucket allows controlled burst traffic.
- Choose the algorithm based on business requirements rather than using the same algorithm everywhere.

---

## Interview Takeaways

Questions an interviewer may ask:

- Why Redis instead of a database?
- Why not store request counts in application memory?
- Difference between Fixed Window and Sliding Window?
- When would you use Token Bucket?
- How would you handle multiple backend servers?