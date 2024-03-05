- redis stack: In addition to all of the features of Redis OSS, Redis Stack supports:
  - Probabilistic data structures
  - Queryable JSON documents
  - Querying across hashes and JSON documents
  - Time series data support (ingestion & querying), including full-text search
- redis-commander: Redis web management tool written in node.js
  ```yaml
  version: '3'
  services:
    redis:
      container_name: redis
      hostname: redis
      image: redis
      ports:
          - "6379:6379"
      links: 
      - redis-commander

    redis-commander:
      container_name: redis-commander
      hostname: redis-commander
      image: rediscommander/redis-commander:latest
      build: .
      restart: always
      environment:
          - REDIS_HOSTS=redis
      ports:
          - "8081:8081"
  ```
- [C# Redis – Implementing Cache Tagging](https://stackify.com/implementing-cache-tagging-redis/)
  - issues:
    - tag-set growth: Redis only allows the expiration of entire entries – expiration cannot be set to a single value within any of Redis’ collection data types.
    - evit
- Redis Smart Cache: java ![Redis Smart Cache](https://raw.githubusercontent.com/redis-field-engineering/redis-smart-cache/master/src/media/redis-smart-cache-flow.png)
