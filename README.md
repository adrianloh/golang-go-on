# Index

[Bytes and Buffer](bytesbuffer.md)

[Regular Expressions](regex.md)

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

## Pointers
```go

type X struct {}

x := X{}
y := &x
z := *y

fmt.Println(x == z) // true
```

Now the inverse:

```go

type A struct {}

a := &A{}
b := *a
c := &b

fmt.Println(c == a) // true
```

When using `fmt.Printf("%T", thing)`, pay attention to the prefix of the type. In the second case, this will print:

```go
*main.A
```

## Strings
---
### strip
```go
import "strings"

line := "     what's with all the extra whitepsace     "
line = strings.Trim(line, " ")
```

### indexOf

Because we're not doing anything with the elements, we don't have too much headache using `reflect` and `interface{}`. So here's a generic implementation:

```go
import "reflect"

func indexOf(list interface{}, elem interface{}) int {
	v := reflect.ValueOf(list)
	for i := 0; i < v.Len(); i++ {
		if v.Index(i).Interface() == elem {
			return i
		}
	}
	return -1
}
```

TIP: If you're "indexing" stuff that needs to be unique but need not be in order, it's faster to just stash them into a hash.

## Slices and Maps
---
### hasKey
The typical ("correct") way:

```go
val, hasKey = o[key]
if hasKey {
	// Do something to val
}
```

Like structs, missing keys are mapped to the zero-value of the declared type of values:

```go
h := &map[string]int{}

h["someKeyThatDoesNotExist"] //=> 0 #Because The zero value of type `int` is 0
```

The zero-value of `bool` is `false` and we can use maps to index an unordered list:

```go
h := map[string]bool{}

h["aa"] = true
h["bb"] = true

if !h["cc"] {
	// "cc" is not in h
}
```

Crazily enough, you can use arbitrary stuff as keys, even structs!

```go
type Hero struct {
	name string
}

avengers := &map[Hero]int{}

ironman := Hero{"Ironman"}
hulk := Hero{"Hulk"}
gadget := Hero{"Inspector Gadget"}

avengers[ironman] := 1
avengers[hulk] := 1

fmt.Printf("%v", avengers[ironman]) //=> 1
fmt.Printf("%v", avengers[gadget])  //=> 0
```




## IO
---
### Write to file

```go

p := "/path/to/a/file"

fd, _ := os.Create(p)
fd.WriteString("Hello World\n")
fd.Close()

// If you're appending (assuming) -- pay attention to the flag

fd, _ = os.OpenFile(p, os.O_RDWR|os.O_APPEND, 0666)
fd.WriteString("Hello Again\n")
fd.Close()

data, _ := ioutil.ReadFile(p)
fmt.Println(string(data))

/* outputs:
	Hello World
	Hello Again
*/

```

### For each file in directory

```go
files, _ := ioutil.ReadDir(folderPath)
for _, fileInfo := range files {
	// Do something with each file
}
```

### Is this a directory or file?
With an `os.FileInfo` object:

```go
if fileInfo.Mode().isDir() {
	// Is a direcotry
}

if fileInfo.Mode().IsRegular() {
	// Is a file
}
```

### Existence
Like *node.js*, just use Stat

```go
fileInfo, err := os.Stat(pathToFile)
if err!=nil {
	// The file does not exists
}
```

### For each line of file
```go
f,_ := os.Open(timecodes)
scan := bufio.NewScanner(f)
for scan.Scan() {
	line := scan.Text()
}
```

### Read entire file
```go
import "io/ioutil"

data, err := ioutil.ReadFile(checkPointFile)
```

### Dump to file
```go
import "io/ioutil"

data := []byte(theStringToDump)
err := ioutil.WriteFile(pathToFile, data, 0644)
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
fileInfo := os.Stat(fullPath) 	 // type is os.FileInfo
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

## Structs

These forms are equivalent:

```go
s := new(X)
t := &X{}

fmt.Printf("%T", s) // *main.X
fmt.Printf("%T", t) // *main.X

```

But these are *not*:

```go
var a X
b := new(X)

fmt.Printf("%T", a) // main.X -- A concrete value
fmt.Printf("%T", b) // *main.X -- A pointer
```

When you make a "concrete" declaration (without a pointer), you cannot leave fields empty!

```go

p3 := Person{id:2}  // Compile ERROR

p3 := &Person{id:2} // OK, name defaults to empty string

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