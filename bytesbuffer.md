## Buff and Buffers

When you need a place to stash bytes but need to pass in an io.Writer

```go
bb := &bytes.Buffer{}
wb64 := base64.NewEncoder(base64.StdEncoding, bb)
```

### bytes.Buffer --> []byte || string

```go
bb := &bytes.Buffer{}

bb.Bytes()

bb.String()
```

### When you have a string but need to pass it in as io.Reader
```go
str := "some bloody long string..."
strings.NewReader(str)
```

Or you can use `bytes.NewBufferString`. E.g `http.NewRequest` expects an `io.Reader` as its last `body` parameter:

```go
	data := "some bloody string"
	req, _ := http.NewRequest("PUT", baseURL, bytes.NewBufferString(data))
	resp, _ := (&http.Client{}).Do(req)
```
