## Strings

### strip
```go
import "strings"

line := "     what's with all the extra whitepsace     "
line = strings.Trim(line, " ")
```

### string -> integer:

```go
import "strconv"

number := "1234"
n := strconv.Atoi(number)

fmt.Printf("%T", n) //=> int
fmt.Printf("%v", n) //=> 1234

```

### integer -> string:

```go
import "strconv"

number := 1234
s := strconv.Itoa(number)

fmt.Printf("%T", s) //=> string
fmt.Printf("%v", s) //=> "1234"

```