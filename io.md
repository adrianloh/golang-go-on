## File IO

### Write text to file

```go

p := "/path/to/a/file"

fd, _ := os.Create(p)
fd.WriteString("Hello World\n")
fd.Close()

```

### Appending to a text file

```go

fd, _ = os.OpenFile(p, os.O_RDWR|os.O_APPEND, 0666)
fd.WriteString("Hello Again\n")
fd.Close()

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

### For each line of file
```go
f,_ := os.Open(timecodes)
scan := bufio.NewScanner(f)
for scan.Scan() {
	line := scan.Text()
}
```