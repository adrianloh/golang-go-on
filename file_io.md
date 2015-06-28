## File IO

### (Over)write to file

```go

p := "/path/to/a/file"

f, _ := os.Create(p)
f.WriteString("Hello World\n")
f.Close()

```

### Append to file

```go

f, _ = os.OpenFile(p, os.O_RDWR|os.O_APPEND, 0666)
f.WriteString("Hello Again\n")
```

### Defer close

When you open files, remember to `defer` the close *after* you check the error.

```go

f, err = os.Open(p)
if err != nil {
	// Handle error
}
defer f.Close()
// Do stuff with file
```

### Dump to file

```go
import "io/ioutil"

data := []byte(theStringToDump)
err := ioutil.WriteFile(pathToFile, data, 0666)
```

### Read from a text file

```go

data, _ := ioutil.ReadFile(p)
fmt.Println(string(data)) // `data` is []byte

```

### For each line of file
```go
f,_ := os.Open(timecodes)
scan := bufio.NewScanner(f)
for scan.Scan() {
	line := scan.Text()
}
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

### Walk

`filepath.Walk` recurses through a directory and you do stuff via a callback function. The callback function is passed 3 arguments and should return either `nil` or an `error`.

```go
func(pathfile string, fifo os.FileInfo, err error) error
```

Now let's say we wanna delete all files over a 100MB.

```go
import(
	"filepath"
	"os"
1)

filepath.Walk("/root/path/to/start/walk", func(path string, fifo os.FileInfo, err error) error {
	if fifo.Mode().IsRegular() && fifo.Size() > 100000000 {
		os.Remove(path)
	}
	return nil
})
```

### Existence
Either `os.Stat` or `os.Open` the file and catch the error:

```go

fileInfo, err := os.Stat(pathToFile)
if err != nil {
	// The file *probably* doesn't exist
}
```

If we want to make sure we *really* got a "does not exist" error:

```go

if f, err := os.Open(pathToFile); os.IsNotExist(err) {
	// Deal with it not existing
}
```