# Pipeline

In addition, pipelines can also ensure the buffered commands are executed atomically as a group. This happens by default. If you want to disable the atomic nature of a pipeline but still want to buffer commands, you can turn off transactions.

```python
>>> pipe = r.pipeline(transaction=False)
```