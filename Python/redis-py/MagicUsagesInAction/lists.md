# Lists

## Multi Pop

```python
def multi_pop(conn, key, num):
def multi_lpop(conn, key, num):
    if num <= 0:
        return [[], False, 0]
    pipe = conn.pipeline()
    pipe.lrange(key, 0, num - 1)
    pipe.ltrim(key, num, -1)
    pipe.llen(key)
    return pipe.execute()
```

## Trim Lpush

```python
def trim_lpush(conn, key, num, *values):
    pipe = conn.pipeline()
    pipe.lpush(key, *values)
    pipe.ltrim(key, 0, num - 1)
    pipe.llen(key)
    return pipe.execute()
```

## Trim Rpush

```python
def trim_rpush(conn, key, num, *values):
    pipe = conn.pipeline()
    pipe.rpush(key, *values)
    pipe.ltrim(key, -num, -1)
    pipe.llen(key)
    return pipe.execute()
```

## References

[1] redisclub/redis-extensions-py@Github, [Redis-extensions is a collection of custom extensions for Redis-py.](https://github.com/redisclub/redis-extensions-py)
