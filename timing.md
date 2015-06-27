## Time and Timing

### Now, Format

```go
import "time"

date := time.Now()

epochTime := date.Unix()

fmt.Printf("======== %s ========\n", date.Format(time.RFC1123))
//=> ======== Sun, 28 Jun 2015 03:01:51 GMT ========
// For other pre-declared formats to pass to `time.Format`, look at the Time package

fmt.Printf("%d-%02d-%02d", date.Year(), date.Month(), date.Day())
//=> 2015-06-28

```

### Parse
```go

resp, _ := http.Head("http://some/file/on/a/server")
last_modfied := res.Header.Get("Last-Modified")
mtime, _ := time.Parse(time.RFC1123, last_modified)

```


### Sleep

```go

doStuff()
time.Sleep(60 * time.Seconds)
doMoreStuff()

```

When passing in a variable to `time.Sleep`, you must declare it as a `time.Duration`:

```go

secsToSleep := time.Duration(60)
time.Sleep(secsToSleep * time.Seconds)

```

### Math

```go

now := date.Now()
tomorrow := date.AddDate(0, 0, 1)
yesterday := date.AddDate(0, 0, -1)
two_months_from_now := date.AddDate(0, 2, 0)
three_years_ago_from_now := date.AddDate(-3, 0, 0) 

```

### Elapsed
```go

start := time.Now()
time.Sleep(time.Second)
fmt.Printf("Completed in %s\n", time.Since(start))

```

### Execution time of function

Within the body of a function, defer the `timeTrack` function

```go

import "runtime"
import "reflect"

func timeTrack(start time.Time, name string) {
    elapsed := time.Since(start)
    log.Printf("%s took %s", name, elapsed)
}

func funcName() string {
    pc, _, _, _ := runtime.Caller(1)
    return runtime.FuncForPC(pc).Name()
}

func funcToTrack() {
	defer timeTrack(time.Now(), funcName())
	// Do shit
}

```