# 01 - URL Shortener

## Problem Statement

Design a URL Shortener like Bitly.

Example:

Long URL:
https://www.amazon.in/product/iphone-16-pro-max...

Short URL:
https://short.ly/AbC123

When a user opens the short URL, they should be redirected to the original URL.

---

## Why do we need a URL Shortener?

- Long URLs are difficult to share.
- Short URLs are easier to remember.
- They are useful for marketing, social media, and QR codes.

---

## MVP (Minimum Viable Product)

The first version should support:

1. Accept a long URL.
2. Generate a unique short URL.
3. Store the mapping between the short URL and the original URL.
4. Redirect the user to the original URL when the short URL is opened.

---

## User Flow

User
↓
Paste Long URL
↓
Server generates Short Code
↓
Store mapping
↓
Return Short URL
↓
User opens Short URL
↓
Server finds Original URL
↓
Redirect User

---

## Learning Notes

- First understand the business problem before designing the system.
- Design the MVP before adding advanced features.
- Think about what happens when the number of users increases.
- More users mean more requests.
- We don't reduce requests; we distribute the load across multiple servers.

## Today's Learnings

- What problem does this application solve?
- What is an MVP?
- Why is a URL Shortener useful?
- Why does system design start with requirements instead of technology?