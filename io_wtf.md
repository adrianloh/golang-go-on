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

### Functions that deal with streams of stuff

Most of Go's function that do i/o are implemented as Readers/Writers.

Whereas in Python you would (kinda) say `base64.encode(data)` or `json.loads(string)`, in Go, you think of them like this...

Let's take `base64.NewEncoder`. The function doesn't say "base64 encode some data". What it says is, "give me a base64 encoder I can write data into". It returns an `io.Writer`.

But before it can give you a "base64 writer", obviously, you have to give it a place to write the encoded data into. So you have to give it an `io.Writer`.

```go
buff := &bytes.Buffer{} // A place for the "base64 writer" to write to

base64_writer := base64.NewEncoder(base64.StdEncoding, buff)

io.Copy(base64_writer, dataReader) // Where dataReader can be any `io.Reader`
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
io.Copy(dst, src) // => Go fuck yourself
```

Under the hood, `io.Copy` does something like this:

```go
buff := make([]byte, maxBuffLength)
src.Read(buff)
dst.Write(buff)
```

### Using io.Pipe to open a file >> zip it >> base64 encode it >> transmit it

Now for something slightly more involved, we're gonna build a pipeline that looks something like this:

```
file | wgzip | wbase64 | wpipe | rpipe | http
```

Sometimes it helps to think of pipes *in reverse* -- start with the thing that comes out last and work your way down to the thing that goes in first.

```go

// The "last" thing is "transmit". `http.NewRequest` needs something to read from.

rp, wp := io.Pipe()

// pipe writer -> pipe reader -> http
req, _ := http.NewRequest("PUT", "http://some/api/upload", rp)

// base64-encoded data -> pipe writer
wb64 := base64.NewEncoder(base64.StdEncoding, wp)

// gzipped data -> base64 encoder
wgzip, _ := gzip.NewWriterLevel(wb64, gzip.BestCompression)

// `io.Copy` blocks but to get the ball rolling, we have to execute
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


