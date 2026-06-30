# 04 - Chat Application System Design (Version 1)

> 🚧 **Work in Progress**

This repository contains the initial system design of a real-time chat application similar to WhatsApp.

The focus of Version 1 is to understand the core architecture, real-time communication, offline messaging, and scalability fundamentals.

Future versions will extend this design with advanced features such as Group Chat, Push Notifications, Multi-device Synchronization, and Media Processing.

---

# Problem Statement

Design a scalable chat application that allows users to exchange messages in real time while supporting millions of concurrent users.

---

# Functional Requirements

* One-to-One Chat
* Send Messages
* Receive Messages
* Offline Message Delivery
* Message Status (Sent, Delivered, Read)
* Online / Offline Status
* Last Seen
* Typing Indicator
* Basic Image/File Sharing

---

# Non-Functional Requirements

* Low Latency
* High Availability
* Scalability
* Fault Tolerance
* Reliable Message Delivery

---

# Minimum Viable Product (MVP)

Version 1 supports:

* User A sends a message.
* User B receives the message instantly if online.
* Messages are stored when the receiver is offline.
* Users can reconnect and receive pending messages.

---

# High-Level Architecture

```text
                Load Balancer
                     │
      ┌──────────────┼──────────────┐
      ▼              ▼              ▼
 Chat Server1   Chat Server2   Chat Server3
        │              │              │
        └──────────────┼──────────────┘
                       │
                    Redis
                       │
                 PostgreSQL
                       │
                Message Queue
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Worker1     Worker2     Worker3
```

---

# Message Flow

```text
User A

↓

WebSocket Connection

↓

Chat Server

↓

Store Message

↓

Return Success

↓

Message Queue

↓

Worker

↓

User B
```

---

# Why WebSocket?

HTTP creates a new connection for every request.

A chat application requires continuous two-way communication between the client and the server.

WebSocket maintains a persistent connection, allowing messages to be delivered instantly without creating a new connection for every request.

---

# Offline Messages

If the receiver is offline:

* Store the message in the database.
* Deliver it when the user reconnects.
* Update the message status after successful delivery.

---

# Database Design

| Column      | Description             |
| ----------- | ----------------------- |
| id          | Unique Message ID       |
| sender_id   | Sender User             |
| receiver_id | Receiver User           |
| message     | Message Content         |
| status      | Sent / Delivered / Read |
| created_at  | Message Timestamp       |

---

# Redis Usage

Redis stores temporary information such as:

* Active WebSocket Connections
* User → Chat Server Mapping
* Online Users

Example:

```text
UserA → Server1

UserB → Server3
```

---

# Background Processing

A chat server should respond quickly to the sender.

Message delivery is processed asynchronously using a Message Queue and Workers.

This keeps request latency low even when many users are receiving messages.

---

# Message Queue

Purpose:

* Decouple message delivery from request processing.
* Process messages in the background.
* Improve scalability and reliability.

---

# Multiple Workers

Instead of one worker processing all deliveries, multiple workers consume tasks from the queue.

Benefits:

* Faster processing
* Better scalability
* Reduced bottlenecks

---

# Typing Indicator

Typing is a temporary event.

It should not be stored permanently in the database.

The server forwards typing events to the receiver and automatically removes the indicator after a short timeout if no new typing event is received.

---

# File Sharing

Large media files should not be stored directly in the database.

Recommended approach:

* Upload files to Object/File Storage.
* Store only the file URL and metadata in the database.

---

# Technologies Discussed

* WebSocket
* Load Balancer
* Redis
* PostgreSQL
* Message Queue
* Background Workers
* Object Storage

---

# Key Learnings

* Use WebSocket for real-time communication.
* Store offline messages for later delivery.
* Use Redis for temporary connection data.
* Keep heavy tasks in the background using queues.
* Scale chat servers and workers independently.
* Store large files outside the database.
* Design systems by identifying bottlenecks first and then choosing the appropriate solution.

---

# Current Progress

## ✅ Completed

* One-to-One Chat
* WebSocket
* Offline Messaging
* Redis Integration (Concept)
* Message Queue
* Multiple Workers
* Typing Indicator
* Basic File Storage Design

## 🚧 Planned

* Group Chat
* Push Notifications
* Multi-Device Synchronization
* Advanced Media Processing
* Final Architecture Improvements
* Performance Optimizations

---

# Interview Questions

* Why WebSocket instead of HTTP?
* Why Redis?
* Why use a Message Queue?
* How are offline messages handled?
* Why shouldn't typing events be stored in the database?
* How would you scale the chat application for millions of users?

---

# Folder Structure

```text
04-chat-application/

└── README.md
```
