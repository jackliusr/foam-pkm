- protocol: RESP2 and RESP3
  - RESP3 can carry out-of-band / “push” messages on a single connection, where-as RESP2 requires a separate connection for these messages
  - RESP3 can (when appropriate) convey additional semantic meaning about returned payloads inside the same result structure
  - Some commands (see this topic) return different result structures in RESP3 mode; for example a flat interleaved array might become a jagged array
- data structure:
  - hashset: 
- database: Redis by default have 16 databases that can be selected by using SELECT command.  since Redis Cluster only supports database zero.
- default ttl: -1, keep forever
- Syntax to flush Redis cache to clear redis cache
  ```bash
  redis-cli FLUSHDB
  redis-cli -n DB_NUMBER FLUSHDB
  redis-cli -n DB_NUMBER FLUSHDB ASYNC
  redis-cli FLUSHALL
  redis-cli FLUSHALL ASYNC
  ```
- redisinsight is free to use: 
  ```bash
  docker run -d --name redisinsight -v redisinsight:/db -p 8001:5540 redislabs/redisinsight:latest
  ```
