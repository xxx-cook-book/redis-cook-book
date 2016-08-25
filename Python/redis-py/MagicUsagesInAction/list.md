# List

## Atomic Multi Pop

```python
def multi_pop(conn, key, num):
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
def trim_lpush(self, conn, key, num, *values):
    pipe = conn.pipeline()
    pipe.lpush(key, *values)
    pipe.ltrim(key, 0, num - 1)
    pipe.llen(key)
    return pipe.execute()
```

## Trim Rpush

```python
def trim_rpush(self, conn, key, num, *values):
    pipe = conn.pipeline()
    pipe.rpush(key, *values)
    pipe.ltrim(key, -num, - 1)
    pipe.llen(key)
    return pipe.execute()
```

## References


