## Bytes and Buffers

Two very basic "conversions":

```go
theString := "some bloody string"

asBytes := []byte(theString)
backToString := string(asBytes)
```

### When you have a string but need an io.Reader

Annoyingly (and for good reason), most standard functions expect an `io.Reader` when passing in strings.

In these cases we can use either: 

```go
import(
	"bytes"
	"strings"
)

stringReader := strings.NewReader(theString)
anotherStringReader := bytes.NewBufferString(theString)
anotherStringReaderAgain := bytes.NewBuffer([]byte(theString))
```

An example is  `http.NewRequest` which takes 3 arguments:

```go
req, err := http.NewRequest(method string, url string, requestBody io.Reader)
```

Now let's say we wanna PUT some text/string:

```go
data := "the string I wanna put..."
req, err := http.NewRequest("PUT", "http://api/gateway", strings.NewReader(data))
```

If the data is binary, let's say from a file:

```go
file, _ := os.Open("file.jpg") // file satisfies `io.Reader`
req, err := http.NewRequest("PUT", "http://api/gateway", file)
```

### When you need to stash bytes but need an io.Writer

Most standard functions that spit out data require an `io.Writer`.

Let's say you wanna `base64` encode some data. You can't just say `base64.Encode(data)`. This doesn't exist in Go. 

Instead you pass in a buffer into which it writes the encoded data.

```go
import(
	"bytes"
)

buff := &bytes.Buffer{} // This satisfies `io.Writer`
wb64 := base64.NewEncoder(base64.StdEncoding, buff) //
wb64.Write([]byte(someString))
wb64.Close()
```

Now, with buff, depending on what we wanna do with it next:

```go
bb.Bytes()	// as []byte

bb.String() // as string
```

















