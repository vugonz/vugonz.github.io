---
date: 2024-12-11T00:19:00Z
author: "Gonçalo"
title: "Coding a CLI application"
description: In HackerSchool we started building an API for internal management. Before building a web frontend we wanted to create a CLI. This post will talk about that CLI! 📟
draft: false
---

In HackerSchool we started building an API for internal management. Before building a web frontend we wanted to create a CLI. This post will talk about that CLI! 

The repository can be found [here. 📂](https://github.com/HackerSchool/hs-cli)

## Language
What language to pick? 🤔 
Python was the first that came to mind, however, at first glance, most CLI libraries seemed to be getting a bit "in the way" by doing more than necessary, probably because a CLI may require packaging with entrypoints. Because of this I decided to go with Golang. 

## Commands
Now, ideally, we'd like to make this as uncomplicated as possible! So let's apply some software patterns. 🧩 

The first pattern is the obvious command pattern. We have commands which is just a fancy term for handler functions!
```go
type Command func(c *Client.client, args ...string) ([]byte, error)
```

Now we have a function which runs a given command and returns an *exit code*.
```go
func RunCommand(c *client.Client, cmd Command, args ...string) int {
    r, err := cmd(c, args...)
    if err != nil {
        // some logic here
        return 1
    }
    return 0
}
```

With our new `AwesomeCommand` a call from `main` is super simple:
```go
func AwesomeCommand(c *client.Client, args ...string) ([]byte, error) {
    if len(args) != 1 && args[0] != "argument" {
        // something
    }
    return []byte("Command was successful!"), nil
}
```
```go
func main() {
    client := client.Client{}
    os.Exit(commands.RunCommand(&client, commands.AwesomeCommand, os.Args...))
}
```

## Authentication
Since this is an API CLI we are bound to run into authentication at some point. For our use case the authentication is session based. So what would we do if, for example, a command requires authentication? 🕵️‍♂️

## Decorators
Let's use another pattern, the decorator! 🎀

We will have a decorator that can either check if the session has ended before running the command (renewing it if it has) or retry the command after renewing the session if the request fails authentication.
Because I couldn't get `net/http/cookiejar` to expose the `Expiry` of a [cookie](https://stackoverflow.com/questions/79079047/net-http-cookiejar-not-storing-cookie-expires-max-age), the latter option had to be used, meaning we always need to make an extra request to know wether our session has expired.
```go
func WithLoginRetry(cmd Command) Command {
	return func(c *client.Client, args ...string) ([]byte, error) {
		r, err := cmd(c, args...)
		if errors.Is(err, client.ErrUnauthorized) {
            // renew session 
			return cmd(c, args...) // and rerun the command
		}
		return r, err
	}
}
```
This function, the decorator, decorates a command, adding new functionality to it. It runs the command and, if it fails with `client.ErrUnauthorized`, renews the session and retries.

If a new command also requires authentication it only needs to use this decorator. The command should know how it must behave, e.g., it should know to return `client.ErrUnauthorized` error for the retry to be made. 

Now a call from `main` of our previous command with authentication looks like this:
```go
func main() {
    client := client.Client{}
    os.Exit(commands.RunCommand(&client, 
                command.WithLoginRetry(SomeCommand), os.Args...)
}
```

## Extras 

Here I'll present some interesting enhancements related to the project.

### Pipe Support
All commands that require a payload expect the path to the files containing the payload. 
```sh
hscli uupdate username payload.json
```
But, what if you're a chad and want to follow the [UNIX philosphy](https://cscie2x.dce.harvard.edu/hw/ch01s06.html) by applying your mad bash skills with our program? We don't want to anger these goated developers. We should implement piping support! 🐧

Editing a user information should be possible the following way:
```sh
hscli uget username | ... | hscli uupdate username
```
In `...` you edit the desired information and feed that into the `uupdate` command.

To enable this we must accept the paylod to be read from the standard input. Let's make use of yet another decorator.
The underlying idea is that commands that require a payload can also expect it from the standard input.
```golang
func DefaultLastArgumentToStdin(cmd Command) Command {
	return func(c *client.Clitechnical bashing skillsent, args ...string) ([]byte, error) {
		if len(args) == 0 {
			args = append(args, "/dev/stdin")
			return cmd(c, args...)
		}

		// if last argument is not an existing file default to stdin
		lastArg := args[len(args)-1]
		if _, err := os.Stat(lastArg); err != nil {
			args = append(args, "/dev/stdin")
			return cmd(c, args...)
		}

		return cmd(c, args...)
	}
}
```
Now, for commands that expect a file path, if the file doesn't exist (that is, the argument is absent or invalid) we simply default to reading the payload from the standard input. The commands need not to be made aware of this, this is the beauty of the decorator pattern and the UNIX philosphy of *everything is a file*!
### Configuration
This is a topic super specific to Golang, but likely of interest if you use strongly typed languages.
Our program must be configured by the user, for example, the API root URL is not static and the user using the CLI will have its own credentials.
We make use of a struct to hold our configuration values 
```golang
type Configuration struct {
	Root          string `json:"root,omitempty"      env:"HS_ROOT"`
	User          string `json:"user,omitempty"      env:"HS_USER"`
	Password      string `json:"password,omitempty"  env:"HS_PASSWORD"`
	CookieJarPath string `json:"cookiejar,omitempty" env:"HS_COOKIEJAR"`
}
```
What are those strings? Those are tags, they can be accessed at runtime using **reflection**. Tags are commonly used by encoding libraries to enable serialization and deserialization of generic data.
The `json` part is what the `encoding/json` library expects to serialize/deserialize JSON from/to our Configuration struct, the first field in the tag maps our struct field to it's JSON key, the `omitempty` option means that the value can be ommited, which is to say, optional.
Now, we can load JSON data into our Configuration struct easily.
```golang
import "encoding/json"

func main() {
    cfg := Configuration{}
    if err := json.NewDecoder("config.json").Decode(&cfg); err != nil {
        // ...
    }
    return 0
}
```
Nice! So we can write our configuration into a file and our program will use it! But  this is super inconvenient. ☝️🤓 I don't want to have my password stored in a file that is always persisted in the system, and I don't want to edit a file everytime I need to update my configuration. We should allow users to configure via environment variables! 🌱

So let us define our own tags to load our configuration from environment variables. These tags will not be used by external libraries, we will use them ourselves!
We define our `env` tag and in it we put the environment variable name that will hold the configuration value. 

This will get messy, reflection is messy 🫠
```golang
func LoadConfig(cfg *Config) error {
	cfgVal := reflect.Indirect(reflect.ValueOf(cfg))
    missingFields = make([]string, 0)
    missingFieldsEnvTag := make([]string, 0)
    for i := 0; i < cfgVal.NumField(); i++ {
        if cfgVal.Field(i).String() == "" {
            missingFields = append(missingFields, cfgVal.Type().Field(i).Name)
            envTag := cfgVal.Type().Field(i).Tag.Get("env")
            missingFieldsEnvTag = append(missingFieldsEnvTag, envTag)
        }
    }
	// populate missing fields
	for i := 0; i < len(missingFields); i++ {
        configurationValue, exists := os.LookupEnv(missingFieldsEnvTag[i])
        if !exists {
            // error, no configuration value!
        }
        cfgVal.FieldByName(missingFields[i]).SetString(configurationValue)
	}
}
```
Golang reflection has two main types, `Value` and `Type`. I won't pretend to understand it deeply, but the idea here is quite simple. We iterate through each field in our Configuration struct and, if the field value is not set, we save its name and its `env` tag. Later we will load the values into the fields using `os.LookupEnv(tag)`.
This clearly doesn't allow for generic configurations, we are only loading strings, but it suffices for our use case! To achieve a more generic configuration parser you will need even messier reflection! I have written a Bencode parser that requires a bit more care, you can [check it here. 📁](https://github.com/vugonz/bencode)

**Observation**: CLI arguments can also load the configuration, and these overwrite the file and environment ones, the snippet above also doesn't take into account loading the configuration from the file.