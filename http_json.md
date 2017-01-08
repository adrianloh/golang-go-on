## http and JSON

To inspect `http.Request` and `http.Response` objects, use `net/http/httputil`

```go
import "net/http/httputil"

dumpBody := true // Be careful if the body is large e.g. image files, etc.

httputil.DumpRequest(request, dumpBody)
httputil.DumpResponse(response, dumpBody)

```

### Basic request

`curl -X GET -H "x-api-key: 123456" https://www.funnybunny.com`

```go
import "net/http"

req, _ := http.NewRequest("GET", "https://www.funnybunny.com", nil)
req.Header.Set("x-api-key", "123456")

response, err := (&http.Client{}).Do(req)
```

### With query parameters

`curl -X GET https://www.funnybunny.com/path?param1=123&param2=456`

```go

import (
	"net/http"
	"net/url"
)

baseURL := "https://www.funnybunny.com/path"

params := url.Values{}
params.Set("param1", "123")
params.Set("param2", "456")

reqURL := fmt.Sprintf("%s?%s", baseURL, params.Encode())

req, _ := http.NewRequest("GET", reqURL, nil)

response, err := (&http.Client{}).Do(req)

```

### With JSON

`curl -X GET -H "Content-Type: application/json" -d '{"api_key":123}' https://www.funnybunny.com/path`

```go

import (
    "bytes"
    "encoding/json"
    "net/http"
)

jsonPayload := &struct {
	ApiKey string `json:"api_key"`
}{"12345"}

b, _ := json.Marshal(jsonPayload)
req, _ := http.NewRequest("GET", "http://localhost/path", bytes.NewBuffer(b))

// If you wanna use Readers/Writers instead:

buff := &bytes.Buffer{}
json.NewEncoder(buff).Encode(jsonPaylod)
req, _ := http.NewRequest("GET", "http://localhost/path", buff)

```

### POST form

`curl -X POST -F "param1=123" -F "param2=456" https://www.funnybunny.com/path`

Same as before, except:

```go

import (
    "net/http"
    "net/url"
)

params := url.Values{}
params.Set("param1", "123")
params.Set("param2", "456")

response, err := http.PostForm("https://www.funnybunny.com/path", params)

```

### POST JSON

`curl -X POST -H "Content-Type: application/json" -d '{"api_key":123}' https://www.funnybunny.com/path`

```go

import (
    "bytes"
    "encoding/json"
    "net/http"
)

jsonPayload := &struct {
	ApiKey string `json:"api_key"`
}{"12345"}

b, _ := json.Marshal(jsonPayload)

http.Post("https://www.funnybunny.com/path", "application/json", bytes.NewBuffer(b))

```

