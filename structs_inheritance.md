## Structs, Types and Inheritance

Given the followings types

```go

type A struct {
	Prop1 string
}

func (a *A) Call() {
	fmt.Println(a.Prop1)
}

type B struct {
	A
	Prop2 string
}

func (b *B) Call() {
	fmt.Println(b.Prop1)
	fmt.Println(b.Prop2)
}

type C struct {
	*B
	Prop1 string
}

func (c *C) Call() {
	c.B.Call()
	fmt.Println(c.Prop1)
}
```

The first field in B is `type A`. These declarations won't work:

```go
b := B{"My"} // Since field `A` of `type A` is missing

// Raises: cannot use promoted field A.Prop1 in struct literal of type B
b := B{Prop1: "Suck", Prop2: "My"}

```
We can ignore the `A` field and explicitly declare `b.Prop2`

```go
b := B{Prop2: "My"}
b.Call()

/*
Outputs: 
	[Empty line] // Since `A` was never declared, b.Prop1 is an empty string
	My
*/

b.Prop1 = "Suck"
b.Call() 

/*
Outputs: 
	Suck
	My
*/

```

The first field of C is a *pointer* to a `type B`

```go
c := C{&b, "Cock"}
c.Call() // which calls c.B.Call 

/*
Outputs: 
	Suck
	My
	Cock
*/




```


















