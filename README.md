# Textra

Textra is a simple and fast tag extractor package that helps to work with structs tags.

Textra parses struct tags and returns them as a slice. It also does provide extra functionality like filtering.

Gathering json tags to feed it some other service is probably the most common usecase of this.

_Initially I built it for another private project, but decided to try to open-source it, since it could be useful in some use-cases_

## Badges

[![Go Reference](https://pkg.go.dev/badge/github.com/Ravcii/textra.svg)](https://pkg.go.dev/github.com/Ravcii/textra)

## Installation

```shell
go get github.com/Ravcii/textra
```

## Examples

Basic usage:

```go
type Tester struct {
	NoTags   bool
	WithTag  string `json:"with_tag,omitempty"`
	WithTags string `json:"with_tags"          sql:"with_tag"`
	SqlOnly  string `sql:"sql_only"`
}

func main() {
	fields := textra.Extract((*Tester)(nil))
	for _, field := range fields {
		fmt.Println(field)
	}
}

```

```
NoTags(bool):[]
WithTag(string):[json:"with_tag,omitempty"]
WithTags(string):[json:"with_tags" sql:"with_tag"]
SqlOnly(string):[sql:"sql_only"]
```

You can look at return types at [go.doc](https://pkg.go.dev/github.com/Ravcii/textra), basically it returns a slice of fields with its types (as strings) and a slice of Tags for each field.

Now let's apply some functions:

```go
fields := textra.Extract((*Tester)(nil)).RemoveEmpty()
```

```
WithTag(string):[json:"with_tag,omitempty"]
WithTags(string):[json:"with_tags" sql:"with_tag"]
SqlOnly(string):[sql:"sql_only"]
```

What if we care only about SQL tags?

```go
fields := textra.Extract((*Tester)(nil)).RemoveEmpty().Only("sql")
```

```
{WithTags string sql:"with_tag"}
{SqlOnly string sql:"sql_only"}
```

_Only() is a bit special as it returns a Field of a different type, with `Tag` rather than `Tags`_

Although it may be redundant, it also parses types a their string representation (for easier comparsion or output, if you need it)

```go
type Types struct {
	intType         int
	byteType        byte
	runeType        rune
	stringType      string
	booleanType     bool
	sliceStringType []string
	mapType         map[string]string
	chanType        chan int
	funcType        func() error
	importType      time.Time
	pointerType     *string
}

func main() {
	fields := textra.Extract((*Types)(nil))
	for _, field := range fields {
		fmt.Println(field.Name, field.Type)
	}
}
```

```
intType int
byteType uint8
runeType int32
stringType string
booleanType bool
sliceStringType slice
mapType map
chanType chan
funcType func
importType time.Time
pointerType *string
```

### TODO

- [x] **Better README.md** _(i hope)_
- [x] Add blacklisting (RemoveFields)
- [ ] Examples for go.dev
- [x] Some sugar for common tags
  - [x] ByName to get tag for each field
  - [x] Omitempty for "\*,omitempty"
  - [x] Ignored for "-"
- [x] Better string representation
- [ ] ...?
