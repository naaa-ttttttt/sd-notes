## Query Optimization

- EXPLAIN — shows execution plan before running a query, reveals if indexes are being used or full table scans are happening
- Avoid SELECT * — fetch only the columns you need, reduces data transfer between DB and application
- LIMIT — restricts rows returned, essential for pagination at the database level
- UNION over OR — OR can prevent index usage, UNION splits into separate indexed queries and combines results
- LIKE operator — pattern matching using % for any characters and _ for one character. Wildcard at start like '%word' cannot use indexes and scans entire table — avoid at scale

---

## Elasticsearch

- Distributed search engine built for fast full text search
- Uses an inverted index — tokenizes documents into words and maps each word to the documents containing it
- Tokenization breaks text into tokens, inverted index maps tokens to their locations
- Works alongside your main database — PostgreSQL stores source of truth, Elasticsearch stores a searchable copy
- Handles fuzzy search, relevance scoring, and partial matches
- Tradeoffs — data duplication, eventual consistency between DB and search index, operational complexity
- Use when SQL LIKE queries are too slow at scale

---

## Cron Jobs in Double Booking

- Cron job runs periodically — say every 5 minutes — to clean up abandoned pending reservations from the database
- Redis TTL expires the reservation in cache, cron job cleans the actual database record
- Together they ensure abandoned bookings release seats back to available automatically
- Without the cron job, pending reservations accumulate in the DB making seats appear unavailable permanently

---

## OCC and Row Level Locking

- Row level locking — pessimistic. Locks the row before access. Only one user can touch it at a time. Safe but slow under high traffic. Risk of deadlocks
- OCC — optimistic. No locking upfront. Uses version numbers. Checks for conflicts at commit time. Fast under low contention but fails under high contention
- High contention systems like Ticketmaster during peak sales → row level locking
- Low contention systems like profile updates → OCC

---

## Distributed Locks

- Same concept as Mutex in Rust but across multiple servers
- Mutex locks shared memory within one machine using Arc<Mutex<T>>
- Distributed lock stores the lock in Redis — a shared external system all servers can see
- One server sets a key in Redis, others wait, server releases key when done
- TTL on the lock prevents deadlocks if the server crashes before releasing

---

## Idempotency

- Making the same request multiple times produces the same result as making it once
- Solved using idempotency keys — a unique ID attached to every request
- If the same key is received twice, the server returns the cached result without processing again
- GET, PUT, DELETE are idempotent by nature. POST is not
- Critical for payment systems — prevents double charging on retried requests

---

## Write Strategies

- Write Through — write to cache and DB simultaneously. Consistent but slower writes. Most advised for critical data
- Write Back — write to cache first, DB later. Fast but risk of data loss if cache crashes
- Write Around — skip cache, write directly to DB. Cache populated on reads only. Good for rarely read data

---

## Stateless vs Stateful Scaling

- Stateless — server remembers nothing. Any server handles any request. Horizontally scalable by nature
- Stateful — server remembers session or context. Tied to specific server. Requires sticky sessions or shared state store like Redis to scale horizontally
- Modern systems push state into dedicated systems like Redis to keep application servers stateless and freely scalable

---

## Consistency Models

- Strong — every read returns latest write immediately. Slowest. Banking, payments
- Causal — related operations stay in correct order. Chat message ordering
- Read your own writes — you always see your own updates immediately. Sending messages, posting comments
- Monotonic read — history never goes backwards. Chat history, timelines
- Eventual — correct eventually not immediately. Fastest. Profile pictures, last seen status, read receipts

---

## Webhook

- Reversed API call — external service calls your server when an event happens instead of you polling
- You register a URL, external service POST to it when something occurs
- Stripe uses webhooks to notify your server of payment success, failure, or abandonment
- More efficient than polling — your server only acts when there is actually something to act on
- Requires your server to be publicly accessible

---

## Reservation TTL and Cron

- Reservation TTL stored in Redis — automatically expires incomplete bookings after set time
- Cron job cleans up expired pending reservations from the database periodically
- Together they prevent seats being permanently locked by abandoned bookings
- TTL handles cache layer, cron handles database layer
