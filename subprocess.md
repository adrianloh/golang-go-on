## Subprocess

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