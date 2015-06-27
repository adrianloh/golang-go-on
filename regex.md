## Regular Expressions
---
To compile a regex

```go
re_compiled, err := regexp.Compile(`201[234]`)
```

This annoyingly also returns an `err`, which we often don't need. 

So instead use:

```go
re_compiled := regexp.MustCompile(`201[234]`)
```

### search/match
```go
re_compiled := regexp.MustCompile(`201[234]`)
if re_compiled.MatchString(line) {
	// Do stuff
}
```
Alternatively, if you don't need/want to precompile the regex

```go
import "regexp"

line := "hello world"
if m, _ := regexp.MatchString(`hello`, line); m {
	// Do something if matches
}

```

### re.findAll
```go
match := re_compiled.FindAllStringSubmatch(line, -1)
if match != nil {
	m := match[0] // You can treat this as you would Python's
	substring := m[0]
	captureGroup1 := m[1]
	captureGroup2 := m[2]
}
```
If there are no capture groups, the matched substring is `match[0][0]`

If there are capture groups, then matched groups are `match[0][1:]`

### re.sub
```go
re := regexp.MustCompile(`a`)
newStr := re.ReplaceAllString("aaaaa", "b") //=> "bbbbb"
```

### re.IGNORECASE
If you want a case-insensitive regex, prefix the regex with `"(?i)"`

```go
name := "Angelina Jolie"
re := regexp.MustCompile(`(?i)^angelina`)  // This will match
```
