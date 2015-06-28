## io.Reader and io.Writer

When we download a file, we are essentially *piping* bytes from an `http.Response.Body` into an `os.File`.

We can do this because `http.Response.Body` implements `io.Reader` and `os.File` implements `io.Writer`.

To *pipe* bytes, we most commonly use `io.Copy`.

So let's download a file:

```go
import (
	"io"
	"net/http"
	"os"
)

file, _ := os.Create("/path/to/save/file.jpg")
response, _ := http.Get("http://some/image/on/the/web.jpg")
io.Copy(file, response.Body)
```

### So wtf does it mean to implement io.Reader/io.Writer?

It just means the struct has methods named Read/Write. That's it.

```go

type A struct{}

func (a *A) Read(b []byte) (n int, err error)
func (a *A) Write(b []byte) (n int, err error)

```

Both methods take a single argument, a `[]byte` -- the bytes to be read from or written into -- and returns `len(b)` -- how many bytes were written/read, and an `error` (which causes the operation to end/abort when not `nil`).

Let's create a Writer that does jack shit.

```go

type Z struct {}

func (self *Z) Write(b []byte) (n int, err error) {
	return len(b), nil
}
```

Now we can plug `Z` into `io.Copy`, same as before:

```go

z := &Z{}
response, _ := http.Get("http://some/image/on/the/web.jpg")
io.Copy(z, response.Body)

```

Now let's get `Z` to implement `io.Reader`, and have it return a single string. And have its `Write` method print out whatever it received.

```go
func (self *Z) Write(b []byte) (n int, err error) {
	fmt.Println(string(b))
	return len(b), nil
}

func (self *Z) Read(b []byte) (n int, err error) {
	copy(b, []byte("Go fuck yourself"))
	return len(b), io.EOF
}

src := &Z{}
dst := &Z{}
io.Copy(w, r) // => Go fuck yourself
```

Under the hood, `io.Copy` does something like this:

```go

buff := make([]byte, 1024)
src.Read(buff)
dst.Write(buff)

```

### Using io.Pipe to open a file >> zip it >> base64 encode it >> transmit it

Now for something slightly more involved, we're gonna build a pipeline that looks something like this:

```
file | wgzip | wbase64 | wpipe | rpipe | http
```

In Go, it's best to think of it *backwards*.

```go

// `rp` is the final part of the pipeline. We "start the engine" when we start reading from `rp`

rp, wp := io.Pipe()

// pipe writer -> pipe reader -> http
req, _ := http.NewRequest("PUT", "http://some/api/upload", rp)

// base64-encoded data -> pipe writer
wb64 := base64.NewEncoder(base64.StdEncoding, wp)

// gzipped data -> base64 encoder
wgzip, _ := gzip.NewWriterLevel(wb64, gzip.BestCompression)

// `io.Copy` is a blocking operation. To jumpstart the pipeline, we have to execute
// the http request at the top *from all the way at the bottom*
go func() {
	f, _ := os.Open(path)
	io.Copy(wgzip, f) // "raw" data from file -> gzip encoder
	f.Close()
	wgz.Close()
	wb64.Close()
	wp.Close()
}()

resp, err := (&http.Client{}).Do(req)
```
