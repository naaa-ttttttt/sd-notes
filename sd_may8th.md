Systems Design Notes
Consistent Hashing
Modulo hashing assigns shards using hash(key) % n. The problem is when you add or remove a shard, almost every key remaps to a different shard, meaning ~80% of data moves in a live system — catastrophic.
Consistent hashing fixes this by placing both shards and keys on a hash ring. Each key belongs to the nearest shard clockwise. When a shard is added, only its immediate neighbour is affected. Only 1/n of data moves instead of nearly all of it.
Virtual nodes go further — each physical shard gets multiple positions on the ring instead of one, ensuring even distribution and preventing any one shard from owning a large arc and becoming a hotspot. Used in production by Cassandra and DynamoDB.

CAP Theorem
A distributed system can only guarantee two of three: Consistency, Availability, and Partition Tolerance. Since network failures are inevitable, Partition Tolerance is non-negotiable — so the real choice is between Consistency and Availability during a partition.

CP — refuses requests when nodes can't sync. Correct data over uptime. Good for banking, medical records.
AP — keeps responding with potentially stale data. Uptime over correctness. Good for social media, content feeds.

Consistency isn't binary. There's a spectrum:

Strong — every read returns the latest write immediately
Causal — related operations stay in order
Read-your-own-writes — you always see your own updates immediately
Monotonic read — history never goes backwards
Eventual — correct eventually, not immediately

In practice, different parts of the same system use different models. A chat app uses causal consistency for message ordering, read-your-own-writes for sending messages, and eventual consistency for profile pictures and last seen status.
