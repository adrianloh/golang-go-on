## Pointers

Some basic math:

```go

type X struct {}

x := X{}
y := &x
z := *y

fmt.Println(x == z) // true
```

Now the inverse:

```go

type X struct {}

a := &X{}
b := *a
c := &b

fmt.Println(c == a) // true
```

When using `fmt.Printf("%T", thing)` -- which prints the Type of `thing` -- pay attention to the asterisk prefix of the Type:

```go

type X struct {}

a := &X{}
b := X{}

fmt.Printf("%T", a) //=> *main.A
fmt.Printf("%T", b) //=>  main.A
```

These forms are equivalent:

```go
s := new(X)
t := &X{}

fmt.Printf("%T", s) // *main.X
fmt.Printf("%T", t) // *main.X

```

But these are *not*:

```go

a := new(X)
var b X

fmt.Printf("%T", a) // *main.X -- A pointer
fmt.Printf("%T", b) //  main.X -- A concrete value
```

When you make a "concrete" declaration, you cannot leave fields empty!

```go

type Z struct {
	a string
	b int
}

z := Z{a:"hello world"}  // Compile ERROR, you must also initialize `b`

z := &Z{a:"hello world"} // OK

fmt.Println(z.b) //=> 0 #The zero-value of `int` type

```


