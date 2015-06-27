## Crypto

### SHA1 the fast way
```go
import "crypto/sha1"

c := sha1.Sum([]byte("Hello World"))
fmt.Printf("%x", c)

```

### From a stream
```go
import "crypto/sha1"

h := sha1.New()
h.Write([]byte("Good Golly"))
h.Write([]byte("Miss Molly"))
fmt.Printf("%x", h.Sum(nil))

```