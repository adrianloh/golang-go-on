# Index

[Bytes and Buffer](bytesbuffer.md)

[Regular Expressions](regex.md)

[Structs and Pointers](structs_pointers.md)

[IO](io.md)

### io.Pipe

```go
rp, wp := io.Pipe()
wb64 := base64.NewEncoder(base64.StdEncoding, wp)
wgz, _ := gzip.NewWriterLevel(wb64, gzip.BestCompression)
go func() {
	f, _ := os.Open(path)
	io.Copy(wgz, f)
	wgz.Close()
	wb64.Close()
	wp.Close()
	f.Close()
}()
b, _ := ioutil.ReadAll(rp)
return fmt.Sprintf("%s", b)
```





## Subprocess
---
### Run a quick command and get STDOUT
```go
stdout, err := exec.Command("diskutil", "info", dev).Output()
```


### Pipe to STDIN
```go
import "os/exec"

func putInClipboard(str string) error {
	stringToDump := []byte(str)
	subprocess := exec.Command("pbcopy")
	in, err := subprocess.StdinPipe()
	if err != nil {
		return err
	}
	if err := subprocess.Start(); err != nil {
		return err
	}
	if _, err := in.Write(stringToDump); err != nil {
		return err
	}
	if err := in.Close(); err != nil {
		return err
	}
	return subprocess.Wait()
}
```

## Crypto
---
### SHA1 the fast way
```go
import "crypto/sha1"

sha1.Sum([]byte("Hello World))
```

### From a stream
```go
h := sha1.New()
h.Write([]byte("Good Golly"))
h.Write([]byte("Miss Molly"))
checksum := fmt.Sprintf("%x", h.Sum(nil))

```

## Inheritance
```go
func getImageInfo(pathToImage string) image.Config {
	file, _ := os.Open(pathToImage)
	imgInfo, _, _ := image.DecodeConfig(file)
	return imgInfo
}

fullPath := "/home/acme/image.jpg"
fileInfo := os.Stat(fullPath)	 // type is os.FileInfo
imgInfo := getImageInfo(fullPath) // type is image.Config
```

Now we can define a struct that "inherits" properties from both "classes"

```go
type SuperImage struct {
	fullpath string
	os.FileInfo
	image.Config
}
```

Fields/methods "inherited" from the two "classes" are directly callable from SuperImage

```go
image := SuperImage{fullPath, fileInfo, imgInfo}

// width and height are fields of image.Config

width, height := image.width, image.height

// Name() and Size() are methods of os.FileInfo

filename := image.Name()
filesize := image.Size()
```



## Closures
---
### Returning functions

```go
func rep(str1 string) func(string, int) string {
	return func(str2 string, repeat int) string {
		x := str1 + " " + str2 + " "
		return strings.Repeat(x, repeat)
	}
}

f := rep("hello")
s := f("world", 2)
fmt.Println(s) //=> hello world hello world 
```

Be careful when passing a *mutable variable* into a closure *within a loop*.

```go
for _,v := range []int{1,2,3} {
	go func() {
		fmt.Println(v)
	}()
}

/*
Outputs:
	3
	3
	3
*/ 
```

Instead, call the closure and pass in the variable

```go
for _,v := range []int{1,2,3} {
	go func(x int) {
		fmt.Println(x)
	}(v)
}

/*
Outputs:
	1
	2
	3
*/ 
```

## Timing
---
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