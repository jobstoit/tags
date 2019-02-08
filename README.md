# Tags
[![Build Status](https://travis-ci.com/jobbitz/tags.svg?branch=master)](https://travis-ci.com/jobbitz/tags)

An interface package for parsing tags in structs simplifying tag based golang reflections

## Usage
Create your custom tag reader simply defining a parser:
```go
func main() {
	obj := Obj{}

	if err := CustomUnmarshaler(&obj); err != nil {
		panic(err)
	}

	if err := CustomScanner(&obj); err != nil {
		panic(err)
	}
}

type Obj struct {
	Val bool `customTag:"yourcase"`
	StVal string `customTag:"required"`
}

func CustomUnmarshaler(obj interface{}) error {
	parser := func(tagVal string) (string, error) {
		switch tagVal {
		case `yourcase`:
			return `true`, nil
		default:
			return ``, errCustom
		}
	}

	return tags.Parse(obj, `customTag`, parser)
}

func CustomScanner(obj interface{}) error {
	scanner := func(tagVal string, value interface{}) error {
		v, ok := value.(string)
		if !ok {
			return nil
		}

		if tagVal == `required` && v == `` {
			return errRequired
		}

		return nil
	}

	return tags.Scan(obj, `customTag`, scanner)
}

```
you get the values of a parent value in the string seperated by a dot:
```go
parser := func(val string) (string, error) {
	allvalues := strings.Split(value, `.`)
	childVal := allvalues[len(allvalues)-1]
	...
```

## Install
Get this package using go get:
```bash
$ go get github.com/jobbitz/tags/...
```
