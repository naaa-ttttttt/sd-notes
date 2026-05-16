

## Dropbox / File Upload System Design

Core Entities
- Item: the file itself — name, size, type, content
- Metadata: information about the file — fileId, name, size, mimeType, uploadedBy, s3Url, status
- User: who owns or uploaded the file

Upload Flow
- Client requests a pre-signed URL from the File Service
- File Service generates a temporary pre-signed URL pointing to S3
- Client uploads the file directly to S3 using that URL — server never touches the raw file
- S3 notifies the system when upload is complete
- File metadata is saved to the database

Why pre-signed URLs
- Your server never handles large file data — S3 does the heavy lifting
- Security is maintained — the URL is temporary and expires
- Reduces load on your application servers

MIME Type
- A label that identifies what kind of data a file contains — image/jpeg, video/mp4, application/pdf
- Tells the system how to handle the file without opening it

---

## Key Technologies

Relational Databases
- Store structured data in tables with rows and columns
- Tables relate to each other via foreign keys
- SQL joins combine data across tables — INNER, LEFT, RIGHT, FULL OUTER
- ACID guarantees — Atomicity, Consistency, Isolation, Durability
- Indexes speed up reads but slow down writes
- Use when data has clear relationships and consistency is critical

Streams and Event Sourcing
- Streams: continuous flow of real time data processed as it arrives — Kafka, AWS Kinesis
- Event sourcing: storing every event that changed state rather than just current state
- Full audit trail, replayability, and debugging benefits
- Message queue: one consumer per message — task disappears after processing
- Stream: multiple consumers read the same event independently

Distributed Locks
- Prevents race conditions when multiple servers access the same resource simultaneously
- Implemented using Redis — one server sets a key, others wait
- TTL prevents deadlocks if the server holding the lock crashes
- Use cases: inventory management, payment processing, seat booking

Distributed Cache
- Spreads cache across multiple machines using consistent hashing
- Redis Cluster and Memcached support distributed caching
- Solves single point of failure and throughput limitations of a single cache server

LSM Trees
- Log Structured Merge Tree — optimized for write heavy workloads
- Writes go to MemTable in memory first, then flushed to SSTables on disk
- Faster writes than B trees but slower reads
- Used by Cassandra and RocksDB

---

## Common Patterns

Background Tasks
- Offload long running operations to background workers via a queue
- Main server stays responsive — returns immediately while work happens separately
- Client tracks progress via polling or webhooks

Managing Contention
- Multiple processes competing for the same resource causes race conditions
- Solutions: distributed locks, optimistic locking, queuing, sharding

Numbers to Know
- RAM access: 100 nanoseconds
- SSD read: 150 microseconds
- Network round trip same datacenter: 500 microseconds
- One server handles: 10,000 to 100,000 requests per second
- One database handles: 1,000 to 10,000 writes per second
- 99.999% availability = only 5 minutes downtime per year

Back of Envelope Formula
- Requests per second = (daily active users x requests per user per day) / 86,400
- Storage per day = number of actions x size per action
- Servers needed = requests per second / what one server handles

---

## High Level Design Phase

- Draw all major components and how they connect
- Define data flow for each key user action
- Show how your architecture satisfies functional requirements
- Make basic technology choices — database type, cache, queue
- Not yet optimizing for scale or handling edge cases — that comes in deep dives
