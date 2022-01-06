# Requests Library and JSON Data

The Requests library has a `json` parameter that allows you to pass a `dict` to a POST call, rather than using the `data` parameter to pass a `string`.

```python
url = 'https://api.github.com/some/endpoint'
payload = {'some': 'data'}

r = requests.post(url, json=payload)
```

## References

- [Requests Docs](https://docs.python-requests.org/en/master/user/quickstart/#more-complicated-post-requests)
