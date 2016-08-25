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

## References


