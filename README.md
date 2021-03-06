# Survey
[![Build Status](https://travis-ci.org/AlecAivazis/survey.svg?branch=feature%2Fpretty)](https://travis-ci.org/AlecAivazis/survey)
[![GoDoc](http://img.shields.io/badge/godoc-reference-5272B4.svg)](https://godoc.org/github.com/AlecAivazis/survey)


A library for building interactive prompts. Heavily inspired by the great [inquirer.js](https://github.com/SBoudrias/Inquirer.js/).



![](https://zippy.gfycat.com/AmusingBossyArrowworm.gif)

```go
package main

import (
    "fmt"
    "gopkg.in/AlecAivazis/survey.v1"
)

// the questions to ask
var qs = []*survey.Question{
    {
        Name:     "name",
        Prompt:   &survey.Input{"What is your name?", ""},
        Validate: survey.Required,
    },
    {
        Name: "color",
        Prompt: &survey.Select{
            Message: "Choose a color:",
            Options: []string{"red", "blue", "green"},
            Default: "red",
        },
    },
}

func main() {
    // the answers will be written to this struct
    answers := struct {
        Name          string                  // survey will match the question and field names
        FavoriteColor string `survey:"color"` // or you can tag fields to match a specific name
    }{}

    // perform the questions
    err := survey.Ask(qs, &answers)
    if err != nil {
        fmt.Println(err.Error())
        return
    }

    fmt.Printf("%s chose %s.", answers.Name, answers.FavoriteColor)
}
```

## Examples
Examples can be found in the `examples/` directory. Run them
to see basic behavior:
```bash
go get github.com/AlecAivazis/survey

# ... navigate to the repo in your GOPATH

go run examples/simple.go
go run examples/validation.go
```

## Prompts

### Input
<img src="https://media.giphy.com/media/3og0IxS8JsuD9Z8syA/giphy.gif" width="400px"/>

```golang
name := ""
prompt := &survey.Input{
    Message: "ping",
}
survey.AskOne(prompt, &name, nil)
```


### Password
<img src="https://media.giphy.com/media/26FmQr6mUivkq71GE/giphy.gif" width="400px" />

```golang
password := ""
prompt := &survey.Password{
    Message: "Please type your password",
}
survey.AskOne(prompt, &password, nil)
```


### Confirm
<img src="https://media.giphy.com/media/3oKIPgsUmTp4m3eo4E/giphy.gif" width="400px"/>

```golang
name := false
prompt := &survey.Confirm{
    Message: "Do you like pie?",
}
survey.AskOne(prompt, &name, nil)
```


### Select
<img src="https://media.giphy.com/media/3oKIPxigmMu5YqpUPK/giphy.gif" width="400px"/>

```golang
color := ""
prompt := &survey.Select{
    Message: "Choose a color:",
    Options: []string{"red", "blue", "green"},
}
survey.AskOne(prompt, &color, nil)
```


### MultiSelect
<img src="https://media.giphy.com/media/3oKIP8lHYFtGeQDH0c/giphy.gif" width="400px"/>

```golang
days := []string{}
prompt := &survey.MultiSelect{
    Message: "What days do you prefer:",
    Options: []string{"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"},
}
survey.AskOne(prompt, &days, nil)
```

## Validation

Validating individual responses for a particular question can be done by defining a
`Validate` field on the `survey.Question` to be validated. This function takes an
`interface{}` type and returns an error to show to the user, prompting them for another
response:

```golang
q := &survey.Question{
    Prompt: &survey.Input{Message: "Hello world validation"},
    Validate: func (val interface{}) error {
        // since we are validating an Input, this will always succeed
        if str, ok := val.(string) ; ok {
            if len(str) > 10 {
                return errors.New("This response cannot be longer than 10 characters.")
            }
        }
    }
}
```

### Built-in Validators
`survey` comes prepackaged with a few validators to fit common situations. Currently these
validators include:

|    name      |   valid types   |       description                                             |
|--------------|-----------------|---------------------------------------------------------------|
| Required     |   any           |   Rejects zero values of the response type                    |
| MinLength(n) |   string        |   Enforces that a response is at least the given length       |
| MaxLength(n) |   string        |   Enforces that a response is no longer than the given length |

## Versioning

This project tries to maintain semantic GitHub releases as closely as possible. As such, services
like [gopkg.in](http://labix.org/gopkg.in) work very well to ensure non-breaking changes whenever
you build your application. For example, importing v1 of survey could look something like

```golang
package main

import "gopkg.in/AlecAivazis/survey.v1"
```
