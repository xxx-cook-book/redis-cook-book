# ``Utf8`` vs. ``Unicode``

```python
In [26]: r.set('codec', u'测试')
Out[26]: True

In [27]: r.get('codec')
Out[27]: '\xe6\xb5\x8b\xe8\xaf\x95'

In [28]: type(r.get('codec'))
Out[28]: str

In [29]: r.set('codec', json.dumps(u'测试'))
Out[29]: True

In [30]: json.loads(r.get('codec'))
Out[30]: u'\u6d4b\u8bd5'

In [31]: type(json.loads(r.get('codec')))
Out[31]: unicode
```

