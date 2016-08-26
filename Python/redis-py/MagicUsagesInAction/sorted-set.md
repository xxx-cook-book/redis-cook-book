# Sorted Set

## Delayed Tasks

```python
# See: https://redislabs.com/ebook/redis-in-action/part-2-core-concepts-2/chapter-6-application-components-in-redis/6-2-distributed-locking/6-2-3-building-a-lock-in-redis
def acquire_lock(conn, lockname, acquire_timeout=10):
    identifier = str(uuid.uuid4())

    end = time.time() + acquire_timeout
    while time.time() < end:
        if conn.setnx('lock:' + lockname, identifier):
            return identifier

        time.sleep(.001)

    return False


def release_lock(conn, lockname, identifier):
    pipe = conn.pipeline(True)
    lockname = 'lock:' + lockname

    while True:
        try:
            pipe.watch(lockname)
            if pipe.get(lockname) == identifier:
                pipe.multi()
                pipe.delete(lockname)
                pipe.execute()
                return True

            pipe.unwatch()
            break
        except redis.exceptions.WatchError:
            pass

    return False


# See: https://redislabs.com/ebook/redis-in-action/part-2-core-concepts-2/chapter-6-application-components-in-redis/6-4-task-queues/6-4-2-delayed-tasks
def execute_later(conn, queue, name, args, delay=0):
    identifier = str(uuid.uuid4())

    item = json.dumps([identifier, queue, name, args])

    if delay > 0:
        conn.zadd('delayed:', time.time() + delay, item)
    else:
        conn.rpush('queue:' + queue, item)

    return identifier


def poll_queue(conn):
    while True:
        item = conn.zrange('delayed:', 0, 0, withscores=True)

        if not item or item[0][1] > time.time():
            time.sleep(.01)
            continue

        item = item[0][0]
        identifier, queue, name, args = json.loads(item)

        locked = acquire_lock(conn, identifier)
        if not locked:
            continue

        # Special for XXX
        if queue == 'xxx':
            print 'XXX: {0}'.format(name)
            # xxx_fun(name)

        if conn.zrem('delayed:', item):
            conn.rpush('queue:' + queue, item)

        release_lock(conn, identifier, locked)
```

## Sorted By Timestamp When Score Equals

```python
def timestamps(self, desc=False):
    stamp = int(time.time() * 1000)
    return self.max_timestamp - stamp if desc else stamp


def rawscore(self, score):
    if not score:
        return 0.0
    return float(int(float(score) / self.rate))


def stampscore(self, score, desc=False):
    return score * self.rate + self.timestamps(desc)


def zaddwithstamps(self, conn, name, *args, **kwargs):
    desc = 'desc' in kwargs and kwargs.pop('desc')
    pieces = [item if index % 2 else self.stampscore(item, desc) for index, item in enumerate(args)]
    for pair in iteritems(kwargs):
        pieces.append(self.timestamps(pair[1], desc))
        pieces.append(pair[0])
        return conn.zadd(name, *pieces)


def zincrbywithstamps(self, conn, name, value, amount=1, desc=False):
    return conn.zadd(name, self.stampscore(self.rawscore(conn.zscore(name, value)) + amount, desc), value)
```

* Usage

  ```python
  In [10]: from redis_extensions import zaddwithstamps, zincrbywithstamps, rawscore

  In [11]: zaddwithstamps(r, 'sorted_set', 1, 'a')
  Out[11]: 1

  In [12]: zincrbywithstamps(r, 'sorted_set', 'b')
  Out[12]: 1

  In [13]: r.zrange('sorted_set', 0, 100, withscores=True)
  Out[13]: [('a', 11472180607330.0), ('b', 11472180610715.0)]

  In [14]: r.zrange('sorted_set', 0, 100, withscores=True, score_cast_func=rawscore)
  Out[14]: [('a', 1.0), ('b', 1.0)]

  In [15]: zincrbywithstamps(r, 'sorted_set', 'b')
  Out[15]: 0

  In [16]: zincrbywithstamps(r, 'sorted_set', 'a')
  Out[16]: 0

  In [17]: r.zrange('sorted_set', 0, 100, withscores=True, score_cast_func=rawscore)
  Out[17]: [('b', 2.0), ('a', 2.0)]
  ```

## References

[1] redisclub/redis-extensions-py@Github, [Redis-extensions is a collection of custom extensions for Redis-py.](https://github.com/redisclub/redis-extensions-py)
