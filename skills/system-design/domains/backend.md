# Backend: APIs, services, storage

## Questions
Ranked; re-rank for the prompt. The default follows each one.

1. Can data ever be wrong, lost, or duplicated? Default: never for money or orders; at-least-once with dedupe elsewhere.
2. Peak traffic? Default: 100 req/s, peak 3x average.
3. Read-heavy or write-heavy? Default: 10 reads per write.
4. Who calls it, and with what auth and roles? Default: logged-in users plus an admin role.
5. Data retention and compliance (GDPR, CCPA, PCI, HIPAA)? Default: delete on request, 1 year of logs.
6. Required cloud, stack, or managed services? Default: whatever the repo already uses.
7. Latency target? Default: p95 under 300 ms.

## Scale dimensions
- Requests/sec: daily active users × requests per user per day ÷ 86,400, then × peak factor.
- Storage: rows per day × row size × retention days, plus indexes (about 1.5x).
- Bandwidth: requests/sec × response size.
- Hot keys: the single row, user, or tenant that gets the most traffic.
- Fan-out: how many downstream calls one request triggers.
- Connections: concurrent clients against the database's connection limit.

## Usual failures
- A database primary goes down or fails over; writes in flight are lost or doubled.
- A slow dependency holds threads or connections until the whole service stalls.
- A client retry after a timeout repeats a write that already succeeded.
- A hot key or tenant overloads one shard or one cache entry.
- Cache stampede: many requests miss at once after an entry expires.
- A schema migration locks a large table, or old and new code disagree during a deploy.
- A queue backs up; consumers fall hours behind.
- Secrets leak through logs, errors, or a public bucket.
- A third-party API rate-limits you or changes its response shape.
