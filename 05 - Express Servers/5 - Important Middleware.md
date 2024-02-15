## express.json()
Parses requests with JSON payloads, making the parsed data available on req.body.
```js
app.use(express.json());
```

## express.urlencoded()
Parses requests with URL-encoded payloads. The extended: true option allows for rich objects and arrays to be encoded into the URL-encoded format, allowing for a JSON-like experience with URL-encoded.

```js
app.use(express.urlencoded({extended: true}));
```