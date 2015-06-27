## Slices and Maps

### indexOf

There's no builtin generic function we can use to iterate over an array but here's one:

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

If we're simply testing for membership in an *unordered list*, it's faster to use a map/hash/dictionary because we can use just about anything as keys in maps, including structs!

```go
type Hero struct {
	name string
}

avengers := &map[Hero]bool{}

ironman := Hero{"Ironman"}
hulk := Hero{"Hulk"}
gadget := Hero{"Inspector Gadget"}

avengers[ironman] = true
avengers[hulk] = true

fmt.Printf("%v", avengers[ironman]) //=> true
fmt.Printf("%v", avengers[gadget])  //=> false
```

### hasKey
The typical ("correct") way:

```go

h := &map[keyType]valueType{}

val, hasKey := h[key]
if hasKey {
	// Do something to val
}
```

Like structs, missing keys are mapped to the zero-value of the declared type of values:

```go
h := &map[string]int{}

h["someKeyThatDoesNotExist"] //=> 0 #Because The zero-value of type `int` is 0
```

The zero-value of `bool` is `false`, so we can lazily do this:

```go
h := &map[string]bool{}

h["aa"] = true
h["bb"] = true

if !h["cc"] {
	// "cc" is not in h
}
```

