# 02 - Pastebin System Design

## Overview

Pastebin is a service that allows users to share text or code through a unique URL. Instead of sending large code snippets directly, users can upload the content and share a generated link.

Example:

Input:

```text
print("Hello World")
```

Output:

```
https://pastebin.com/AbC123
```

Anyone with the link can access the stored content.

---

# Problem Statement

Design a scalable Pastebin service capable of storing text/code and serving millions of users efficiently.

---

# Functional Requirements

- Create a new paste.
- Generate a unique URL.
- Retrieve a paste using the generated URL.
- Store text or code.

---

# Non-Functional Requirements

- Low response time.
- High availability.
- Scalable architecture.
- Reliable storage.
- Handle millions of read requests.

---

# MVP (Minimum Viable Product)

The first version should support:

- Create Paste
- Generate Unique ID
- Store Paste
- Retrieve Paste

---

# User Flow

```text
User
 │
 ▼
Enter Text / Code
 │
 ▼
Click Create
 │
 ▼
POST /paste
 │
 ▼
Generate Unique ID
 │
 ▼
Save in Database
 │
 ▼
Return Shareable URL
 │
 ▼
Another User Opens URL
 │
 ▼
GET /paste/{id}
 │
 ▼
Fetch Data
 │
 ▼
Display Content
```

---

# API Design

## Create Paste

```
POST /paste
```

Request

```json
{
  "content": "print('Hello World')"
}
```

Response

```json
{
  "id": "AbC123",
  "url": "https://pastebin.com/AbC123"
}
```

---

## Get Paste

```
GET /paste/{id}
```

Response

```json
{
  "content": "print('Hello World')"
}
```

---

# Database Design

| Column | Purpose |
|----------|----------|
| id | Unique Paste Identifier |
| content | User Text / Code |
| created_at | Creation Timestamp |

---

# Initial Architecture

```text
User
 │
 ▼
Browser
 │
 ▼
Backend API
 │
 ▼
Database
```

This architecture works well for small traffic.

---

# Scaling Journey

## Problem 1

A single backend server cannot handle millions of concurrent users.

### Solution

Introduce a Load Balancer and multiple backend servers.

```text
Users
 │
 ▼
Load Balancer
 │
 ├───────────┐
 ▼           ▼
Server1   Server2
      │
      ▼
 Database
```

---

## Problem 2

Multiple servers now send requests to the same database.

The database becomes the bottleneck.

### Solution

Introduce Redis Cache.

```text
Users
 │
 ▼
Load Balancer
 │
 ▼
Backend Servers
 │
 ▼
Redis Cache
 │
 ▼
Database
```

Benefits

- Faster reads
- Reduced database load
- Better response time

---

## Problem 3

User edits a paste.

Database contains new data.

Cache still contains old data.

### Solution

Update or invalidate the cache after every database update.

This prevents stale data from being served.

---

## Problem 4

The database becomes a single point of failure.

### Solution

Use Database Replication.

```text
Primary Database
     │
 ┌───┴────┐
 ▼        ▼
Replica1 Replica2
```

Benefits

- Improved availability
- Better fault tolerance

Limitation

Replication does **not** solve storage growth.

---

## Problem 5

The database size keeps growing.

A single database cannot efficiently store all records forever.

### Solution

Shard (Partition) the database.

```text
DB1 → A-H

DB2 → I-P

DB3 → Q-Z
```

Benefits

- Storage distributed across databases
- Better scalability
- Lower load on each database

---

# Technologies Discussed

- REST APIs
- Load Balancer
- Redis Cache
- Database Replication
- Database Sharding

---

# Key Learnings

- Start with a simple architecture.
- Scale only when a bottleneck appears.
- Load Balancer distributes incoming traffic.
- Redis reduces database reads.
- Cache must stay consistent with the database.
- Replication improves availability.
- Sharding solves storage and scaling problems.
- Every architectural decision should solve a real problem.

---

# Interview Questions

- Why do we generate a unique ID?
- Why not store everything directly in memory?
- Why is Redis used?
- Difference between Replication and Sharding?
- Why does the database become a bottleneck?
- What happens if the database crashes?
- How would you scale Pastebin for millions of users?

---

# Folder Structure

```
03-pastebin-system-design/
└── README.md
```