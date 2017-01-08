### Timers
Example of a ticker that increments `i` every second, and a second timer that resets it every 10 seconds.

```go
tickerInsert := time.NewTicker(1 * time.Second).C
tickerFlush := time.NewTicker(10 * time.Second).C
i := 0
for {
	select {
	case <-tickerInsert:
		i += 1
		fmt.Println(i)
	case <-tickerFlush:
		i = 0
	}
}
```

### Inheritance
Structs can "inherit" fields and methods from other structs:

```go
// A function that gets info on some image
func getImageInfo(pathToImage string) image.Config {
	file, _ := os.Open(pathToImage)
	imgInfo, _, _ := image.DecodeConfig(file)
	return imgInfo
}

fullPath := "/home/acme/image.jpg"
fileInfo := os.Stat(fullPath) // type is os.FileInfo
imgInfo := getImageInfo(fullPath) // type is image.Config
```

Now define a struct that "inherits" properties from both "classes"

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

### Closure

Be careful when passing a *mutable variable* into a closure *within a loop*.

```go
// Launch a goroutine for each item in the number list
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

