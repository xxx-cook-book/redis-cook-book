# String

## Atomic Get & Delete

```python
def get_delete(conn, key):
    pipe = conn.pipeline()
    pipe.get(key)
    pipe.delete(key)
    return pipe.execute()
```

## Atomic Get & Rename

```python
def get_rename(conn, key, suffix='del'):
    pipe = conn.pipeline()
    pipe.get(key)
    pipe.rename(key, '{}_{}'.format(key, suffix)) if conn.exists(key) else pipe.exists(key)
    return pipe.execute()
```

## References

[1] redisclub/redis-extensions-py@Github, [Redis-extensions is a collection of custom extensions for Redis-py.](https://github.com/redisclub/redis-extensions-py)
