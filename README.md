# Secrets bulk upload with Durable Object binding minimal repro

`wrangler secret bulk` doesn't work with a worker that binds to a DO from another worker.

```bash
pnpm i
pnpm run deploy
pnpm run upload-secrets
```

Result:
```
✘ [ERROR] 🚨 1 secrets failed to upload
```

After modifying the wrangler cli script a bit:

```js
	...
  logger.debug("-- END CF API REQUEST");
  const r = await (0, import_undici3.fetch)(`${getCloudflareApiBaseUrl()}${resource}${queryString}`, {
    method,
    ...init3,
    headers,
    signal: abortSignal
  });
	r.clone().text().then((text) => console.log(r.status, text));
	return r;
}
```

Error:

```
status: 400

body: {
  "result": null,
  "success": false,
  "errors": [
    {
      "code": 10061,
      "message": "Cannot create binding for class 'MyDurableObject' that is not exported by the script"
    }
  ],
  "messages": []
}
```
