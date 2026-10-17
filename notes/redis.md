# Intro

- Redis (Remote Dictionary Server) is an open-source, in-memory data structure store
- All data is stored in RAM → lightning-fast access
- Uses cases are Caching, Session storage, Queueing system, Rate limiting

Data persistence

- RDB (Redis Database Backups)- Periodic snapshots of your dataset
- AOF (Append Only File)- Logs every write operation

Data types

- String
- Hash- like a JS object
- List- Ordered list of strings
- Set- Unordered unique string collection
- Sorted Set (ZSet)- Like a Set, but with scores for sorting
- Stream- Append-only log of events/messages

Commands

```bash
SET key value              # Set value for a key
GET key                    # Get value of a key
DEL key                    # Delete a key
EXPIRE key seconds         # Set time-to-live for a key
TTL key                    # Get remaining TTL of a key
INCR key                   # Increment integer value by 1
DECR key                   # Decrement integer value by 1
APPEND key value           # Append to an existing string
MGET key1 key2 ...         # Get multiple keys at once
MSET key1 val1 key2 val2   # Set multiple keys at once
```

Example

```typescript
import { Injectable, OnModuleInit, OnModuleDestroy } from '@nestjs/common';
import Redis from 'ioredis';

@Injectable()
export class RedisService implements OnModuleInit, OnModuleDestroy {
  private client: Redis; // Redis client instance

  /**
   * Called when the module is initialized.
   * Sets up the Redis client connection.
   */
  onModuleInit() {
    this.client = new Redis(); // Connects to Redis at localhost:6379 by default

    this.client.on('connect', () => {
      console.log('✅ Redis connected');
    });

    this.client.on('error', (err) => {
      console.error('❌ Redis error:', err);
    });
  }

  /**
   * Called when the module is destroyed.
   * Gracefully closes the Redis connection.
   */
  onModuleDestroy() {
    this.client.quit();
  }

  /**
   * Set a key-value pair in Redis.
   * Optionally set a TTL (in seconds).
   * 
   * @param key Redis key
   * @param value Redis value
   * @param ttlInSec Optional TTL in seconds
   * @returns 'OK' if successful
   */
  async set(key: string, value: string, ttlInSec?: number): Promise<string> {
    if (ttlInSec) {
      // Set value with expiration
      return this.client.set(key, value, 'EX', ttlInSec);
    }
    // Set value without expiration
    return this.client.set(key, value);
  }

  /**
   * Get the value of a key from Redis.
   * 
   * @param key Redis key
   * @returns Value or null if not found
   */
  async get(key: string): Promise<string | null> {
    return this.client.get(key);
  }

  /**
   * Delete a key from Redis.
   * 
   * @param key Redis key
   * @returns Number of keys removed (0 or 1)
   */
  async del(key: string): Promise<number> {
    return this.client.del(key);
  }

  /**
   * Set the expiration time on a key.
   * 
   * @param key Redis key
   * @param seconds Time-to-live in seconds
   * @returns 1 if TTL set, 0 if key not found
   */
  async expire(key: string, seconds: number): Promise<number> {
    return this.client.expire(key, seconds);
  }

  /**
   * Get the remaining time-to-live of a key.
   * 
   * @param key Redis key
   * @returns TTL in seconds, -2 if key does not exist, -1 if key exists but has no TTL
   */
  async ttl(key: string): Promise<number> {
    return this.client.ttl(key);
  }
}
```

| Redis | Memcached |
| --- | --- |
| Data types- Strings, Lists, Hashes, Sets, Sorted Sets, Streams | Only Strings |
| Data Persistence- RDB, AOF | No |
| Pub/Sub, TTL, Lua scripts, Streams | Basic GET/SET |
| Max Value Size- 512 MB | 1 MB |
| Supports master-slave replication | No replication |
