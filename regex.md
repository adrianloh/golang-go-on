## Regular Expressions

To compile a regex

```go

import "regexp"

re_compiled, err := regexp.Compile(`201[234]`)
```

This annoyingly also returns an `err`, so we can't declare multiple regexes in a `var` block. 

Instead we can use:

```go

import "regexp"

var(
	re_compiled_1 = regexp.MustCompile(`1234`)
	re_compiled_2 = regexp.MustCompile(`5678`)
)
```

### search/match

Check if the *compiled* regex matches a string:

```go

line := "hello world"

if re_compiled.MatchString(line) {
	// Do stuff
}
```

Alternatively, to match a non-precompiled regex against a string:

```go

import "regexp"

line := "hello world"

if m, _ := regexp.MatchString(`hello`, line); m {
	// Do stuff
}

```

### re.findAll
```go
match := re_compiled.FindAllStringSubmatch(line, -1) // `-1` means "match multiple"

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
