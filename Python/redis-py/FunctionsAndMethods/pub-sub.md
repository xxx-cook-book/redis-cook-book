# Pub/Sub

## Pub

```python
publish(channel, message)
```

Publish `message` on `channel`. Returns the number of subscribers the message was delivered to.

```python
In [31]: r.publish('test', 'this will reach the listener')
Out[31]: 1L
```

## Sub

```python
pubsub(shard_hint=None)
```

Return a Publish/Subscribe object. With this object, you can subscribe to channels and listen for messages that get published to them.

```python
In [3]: pubsub = r.pubsub()

In [4]: pubsub.subscribe('test')

In [5]: pubsub.get_message()
Out[5]: {'channel': 'test', 'data': 1L, 'pattern': None, 'type': 'subscribe'}

In [6]: pubsub.get_message()

In [7]: pubsub.get_message()
Out[7]:
{'channel': 'test',
 'data': 'this will reach the listener',
 'pattern': None,
 'type': 'message'}

In [8]: pubsub.subscribed
Out[8]: True

In [9]: pubsub.
pubsub.PUBLISH_MESSAGE_TYPES      pubsub.ignore_subscribe_messages
pubsub.UNSUBSCRIBE_MESSAGE_TYPES  pubsub.listen
pubsub.channels                   pubsub.on_connect
pubsub.close                      pubsub.parse_response
pubsub.connection                 pubsub.patterns
pubsub.connection_pool            pubsub.psubscribe
pubsub.decode_responses           pubsub.punsubscribe
pubsub.encode                     pubsub.reset
pubsub.encoding                   pubsub.run_in_thread
pubsub.encoding_errors            pubsub.shard_hint
pubsub.execute_command            pubsub.subscribe
pubsub.get_message                pubsub.subscribed
pubsub.handle_message             pubsub.unsubscribe
```

* ``get_message`` will get a ``'type': 'subscribe'`` message after subscribe

## ``get_message()`` vs. ``listen()`` vs. ``callback``

 ## Principle

Redis does not queue anything on pub/sub channels. On the contrary, it tends to read the item from the publisher socket, and write the item in all the subscriber sockets, ideally in the same iteration of the event loop. Nothing is kept in Redis data structures. [More.](http://stackoverflow.com/questions/27745842/redis-pubsub-and-message-queueing)

## References

[1] redis-py@Github, [redis-py](https://github.com/andymccurdy/redis-py/blob/master/redis/client.py)

[2] redis-py@ReadTheDocs, [redis-py’s documentation](https://redis-py2.readthedocs.io/en/latest/)

[3] Marco Benvoglio@StackOverflow, [Redis Pubsub and Message Queueing](http://stackoverflow.com/questions/27745842/redis-pubsub-and-message-queueing)