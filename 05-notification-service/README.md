# 05 - Notification Service System Design (Version 1)

> 🚧 **Work in Progress**

This repository contains the initial system design of a scalable Notification Service used by multiple applications such as Chat, E-commerce, Banking, and Social Media platforms.

The goal of Version 1 is to understand how notifications are generated, processed, and delivered reliably using asynchronous architecture.

Future versions will cover large-scale campaigns, dead-letter queues, analytics, notification templates, and multi-region deployments.

---

# Problem Statement

Design a Notification Service capable of delivering notifications through multiple channels while handling millions of users efficiently.

---

# Functional Requirements

* Send Push Notifications
* Send Email Notifications
* Send SMS Notifications
* Support Offline Users
* Store Notification History
* Retry Failed Notifications
* Scheduled Notifications (Reminders)
* User Notification Preferences
* Unread Notification Count

---

# Non-Functional Requirements

* High Availability
* Low Latency
* Scalability
* Reliable Delivery
* Fault Tolerance

---

# Minimum Viable Product (MVP)

Version 1 supports:

* Applications send notification requests.
* Notifications are processed asynchronously.
* Users receive notifications through the appropriate channel.
* Failed deliveries are retried.
* Notifications are stored for later viewing.

---

# High-Level Architecture

```text
Chat Service
Order Service
Payment Service
Social Media
        │
        ▼
Notification Service
        │
        ▼
Message Queue
        │
 ┌──────┼─────────┐
 ▼      ▼         ▼
Push   Email     SMS
Worker Worker    Worker
        │
        ▼
     End User
```

---

# Request Flow

```text
Application

↓

Notification Service

↓

Queue

↓

Worker

↓

Notification Delivered
```

---

# Why Message Queue?

A notification request should return quickly without waiting for email, SMS, or push delivery.

The Notification Service places the task into a queue, allowing workers to process it asynchronously.

Benefits:

* Faster API responses
* Better scalability
* Improved fault tolerance

---

# Multiple Workers

Different notification channels require different processing logic.

Examples:

* Push Worker
* Email Worker
* SMS Worker

Each worker independently processes tasks from the queue.

---

# Retry Mechanism

Temporary failures should not result in lost notifications.

Examples:

* Email provider unavailable
* SMS gateway timeout
* Network failure

Workers retry failed notifications after increasing time intervals instead of retrying continuously.

---

# User Preferences

Users can control which notifications they receive.

Examples:

* Promotional Emails ON/OFF
* SMS ON/OFF
* Push Notifications ON/OFF

Workers verify user preferences before sending the notification.

---

# Priority Queues

Not every notification has the same importance.

Examples:

High Priority

* OTP
* Payment Failed
* Security Alerts

Medium Priority

* Order Shipped
* Order Delivered

Low Priority

* Promotional Offers
* Marketing Campaigns

High-priority notifications should be processed before low-priority notifications.

---

# Notification History

Notifications are stored in the database so users can view them later.

Example fields:

| Column     | Description        |
| ---------- | ------------------ |
| id         | Notification ID    |
| user_id    | Receiver           |
| title      | Notification Title |
| status     | Delivered / Read   |
| created_at | Timestamp          |

---

# Redis Usage

Redis stores temporary data such as unread notification counts.

Example:

```text
User1 → 8

User2 → 2

User3 → 15
```

When a notification is marked as read, Redis is updated immediately.

---

# Scheduled Notifications

Some notifications must be delivered in the future.

Examples:

* Interview Reminder
* Medicine Reminder
* Birthday Reminder

Flow:

```text
Database

↓

Scheduler

↓

Queue

↓

Worker

↓

User
```

The Scheduler periodically checks notifications whose scheduled time has arrived and sends them to the queue.

---

# Duplicate Notification Prevention

Each notification request should contain a unique identifier.

The Notification Service checks whether the request has already been processed.

If the request already exists, duplicate delivery is avoided.

---

# Delivered vs Read

Delivered means the notification reached the user's device.

Read means the user actually opened the notification.

Unread counts should decrease only after the notification is read.

---

# Technologies Discussed

* Message Queue
* Background Workers
* Redis
* Database
* Scheduler (Cron Job)
* Push Notifications
* Email Notifications
* SMS Notifications

---

# Key Learnings

* Keep notification delivery asynchronous.
* Use queues to improve scalability.
* Assign dedicated workers for different notification channels.
* Retry temporary failures.
* Respect user notification preferences.
* Store notification history for users.
* Cache unread counts using Redis.
* Schedule future notifications using a scheduler.
* Prevent duplicate notifications using idempotency.
* Differentiate between delivered and read notifications.

---

# Current Progress

## ✅ Completed

* Notification Service Architecture
* Queue & Workers
* Push / Email / SMS Notifications
* Retry Mechanism
* User Preferences
* Priority Queues
* Notification History
* Redis for Unread Count
* Scheduled Notifications
* Idempotency
* Delivered vs Read

## 🚧 Planned

* Dead Letter Queue (DLQ)
* Batch Notification Processing
* Notification Templates
* Analytics & Delivery Metrics
* Multi-Region Deployment
* Third-Party Provider Integration

---

# Interview Questions

* Why should notifications be asynchronous?
* Why use a Message Queue?
* Why have separate workers for Push, Email, and SMS?
* How do you retry failed notifications?
* How do you prevent duplicate notifications?
* Why use Redis for unread counts?
* How would you implement reminder notifications?
* How do you prioritize critical notifications?

---

# Folder Structure

```text
05-notification-service/

└── README.md
```
