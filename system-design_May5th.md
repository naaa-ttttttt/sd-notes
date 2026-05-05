# Systems Design Notes — May 5, 2026

## Topics Covered
Networking Essentials, APIs & Types, Data Modelling, Caching, Sharding

---

## Networking Essentials
- The internet is built on layers — each layer has a job (physical transmission, routing, reliability, application)
- TCP vs UDP: TCP guarantees delivery and order, UDP is faster but unreliable — use TCP for chat, UDP for video/gaming
- HTTP is request-response over TCP — client always initiates
- DNS translates human-readable domain names into IP addresses
- WebSockets upgrade an HTTP connection into a persistent two-way channel — no polling needed
- Latency vs bandwidth: latency is the delay, bandwidth is the throughput — both matter differently depending on the use case

---

## APIs & Types
- REST: stateless, resource-based, uses HTTP verbs (GET, POST, PUT, DELETE) — most common, easy to cache
- GraphQL: client specifies exactly what data it wants — avoids over-fetching and under-fetching
- gRPC: uses Protocol Buffers (binary), faster and strongly typed — good for internal service-to-service communication
- WebSocket: persistent connection for real-time two-way communication — not request-response
- Rule of thumb: REST for public APIs, gRPC for internal microservices, GraphQL when clients have varying data needs, WebSocket for real-time features

---

## Data Modelling
- Normalization: eliminate redundancy by splitting data into related tables — good for consistency, can be slow for reads
- Denormalization: duplicate data intentionally for faster reads — trades consistency for performance
- SQL vs NoSQL: SQL is structured and relational with ACID guarantees, NoSQL is flexible and scales horizontally
- Choose your data model based on access patterns — how you read data matters as much as how you store it
- Indexes speed up reads by creating a lookup structure — but they slow down writes, so don't over-index

---

## Caching
- Cache sits between your application and your database — serves frequently accessed data from memory (fast) instead of disk (slow)
- Cache hit: data found in cache. Cache miss: not found, must fetch from DB and populate cache
- Write strategies:
  - Write-through: write to cache and DB together — consistent but slower writes
  - Write-back: write to cache first, DB later — fast writes but risk of data loss
  - Write-around: skip cache on write, write directly to DB — cache stays clean but first read is always a miss
- Eviction policies: LRU (Least Recently Used) is the most common — drop what hasn't been used lately
- CDN is a cache for static assets (images, JS, CSS) distributed geographically close to users

---

## Sharding
- Sharding splits a large database horizontally across multiple machines — each shard holds a subset of the data
- Why: a single DB can only scale so far vertically (bigger machine) — sharding scales horizontally (more machines)
- Shard key determines how data is divided — choosing it badly causes hotspots (one shard getting all the traffic)
- Consistent hashing helps distribute data evenly and minimizes reshuffling when nodes are added or removed
- Sharding adds complexity — cross-shard queries, joins, and transactions become hard problems

---

## Key Connections
- Caching and sharding both exist to solve scale — caching reduces DB load, sharding distributes it
- Your API type affects your data model — GraphQL needs flexible schemas, REST maps well to relational tables
- WebSockets + caching + sharding is exactly the stack a system like WhatsGud needs at scale

---

*Session: ~3-5 hrs | Platform: Hello Interview | Streak: Day 1*
