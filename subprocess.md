## Subprocess

### Quick and dirty run a command

```go
import "os/exec"

err := exec.Command("touch", "/path/to/some/file").Run()
```

Use `strings.Fields` to break a string of arguments into a list/slice you can then pass to `exec.Command`:

```go
import "strings"

argsStr := "-i - -c:v libx265 -preset medium -x265-params crf=28 -an -f m4v -"
args := strings.Fields(argsStr)
cmd := exec.Command("ffmpeg", args...)
```

### os.popen().read()

```go
stdout, _ := exec.Command("echo", "Hello World").Output()
fmt.Println(string(stdout)) //=> Hello World
```

### Read STDERR

You can also simply redirect the command's STDOUT/STDERR to `os.Stdout` or `os.Stderr` respectively.

```go

mov := "/path/to/video.mp4"
ffmpeg := exec.Command("ffmpeg", "-i", mov)

ffmpeg.Stdout = os.Stdout
ffmpeg.Stderr = os.Stdout

```

Command's STDIN/STDOUT/STDERR are implemented as Readers/Writers so you can pipe them around.

```go

mov := "/path/to/video.mp4"
ffmpeg := exec.Command("ffmpeg", "-i", mov)

stderrPipe, _ := ffmpeg.StderrPipe()

buff := &bytes.Buffer{}

ffmpeg.Start()
io.Copy(buff, stderrPipe)
ffmpeg.Wait()

fmt.Println(buff.String())

```

### tail -f

Read a continuous stream from STDOUT

```go
cmd := exec.Command("sh", "output.log")
out, _ := cmd.StdoutPipe()
scan := bufio.NewScanner(out)
cmd.Start()
go func() {
	for scan.Scan() {
		line := scan.Text()
		fmt.Println(line)
	}
}()
cmd.Wait()
```

### Piping

Let's build this pipe:

```
http GET video | ffmpeg | file on disk
```

We need three things:

1. A subprocess of `ffmpeg` that reads from STDIN and writes to STDOUT
2. http.GET of some video and pipe it into ffmpeg's STDIN
3. Create a file on disk and write from ffmpeg's STDOUT into it

```go
argsStr := "-i - -c:v libx265 -preset medium -x265-params crf=28 -an -f m4v -"
args := strings.Fields(argsStr)
cmd := exec.Command("ffmpeg", args...)

in, _ := cmd.StdinPipe()
out, _ := cmd.StdoutPipe()

// Get the video
resp, _ := http.Get("http://bucket.s3.amazonaws.com/tricia.m2t")

// Spawn ffmpeg inside a "hanging" process which waits for input from STDIN
cmd.Start() 

go func() {
	io.Copy(in, resp.Body) // Start writing http.Response.Body to ffmpeg's STDIN
	in.Close() // We close STDIN manually or ffmpeg will wait forever
}()

fout, _ := os.Create("out.m4v")

io.Copy(fout, out) // Write STDOUT to disk

cmd.Wait()

```

