# binaries

## Introduction

A binary file is a computer file that stores information in a non-text format. Instead of using human-readable characters, it uses a sequence of bits and bytes (0s and 1s) that are directly interpretable by a computer's processor.

There are a range of different uses for Binary files, however, we will be focusing on it's use for **Executable Programs/Applications**.

If you think about your terminal, you often use applications like `ls`, `cat`, `pwd`, etc. These are all binaries that can be executed by your operating system to run a set of instructions.

In Go we can fairly easily create these by using `packages` and the go tooling.

## Getting started

To create a new binary, you need to satisfy the following requirements:

* The package is named `main`.
* Your package contains the following function: `main()`

Best practice in Go is to have the following structure:

```
cmd/
  binary_one/
    main.go
  binary_two/
    main.go
```

You might notice that the directory names (`binary_one` and `binary_two`) do not match the package names (`main`). This is one of the few exceptions to the rule where these two things do not correspond. For everything else, it is best practice to keep the directory and package name the same.

We can build the above binaries simultaneously using the following command:

```sh
go build -o . ./...
```

To breakdown the command:

* `go build` - The `go` build tool to compile applications
* `-o .` - We're outputting all of our binaries files in the current directory
* `./...` - We're telling the build tool to scan all of the directories for packages that satisfy the criteria to build a binary.

Once we've run the command, we will end up with two binary files:

```
binary_one
binary_two
```

Each of these files can now be executed via terminal by simply calling them:

```sh
./binary_one
#or
./binary_two
```

Common practice is to use an output folder to build these binaries to, for example `bin`. This ensures that you can easily `gitignore` all of your binary files by simply adding `bin` to your `.gitignore` file. We never want to commit binary files to the repository as they are OS/Architecture specific. A binary built with MacOS using an Apple Chip will not run on a Linux Server. To build to a `bin` directory, your command will change slightly to be:

```sh
go build -o bin ./...
```

## Importing Code

Each of these binaries can import code that exists somewhere else in your repository, for example from `internal` or `pkg`. However, you should not define something within a binary package and re-use it in another binary package. For example, say we create a function in the `cat` binary package to do something specific. If we ever need to use that function in a different binary, such as the `pwd` one, we should move this logic into a centralised package that exists in `internal` or `pkg`.

## You might be asking

![But Why Gif](https://media.tenor.com/jJQshsJcCxsAAAAM/butwhy-why.gif)

This approach is very handy when you need to extend your service with features that should work independently from your core functionality. Examples are:

* Workers - Applications that handle tasks offloaded from your API for async processing. (This will run alongside the API)
* Migrations - An application/script to perform database migrations
* Seeding - An application/script to load necessary static data to your database
* E2E Tests - An application/script that can be run to test the entire workflow of your service. For example, this might replicate the behavior of external systems interacting with your API.

Not only will building all of the above into a single application create a very messy codebase, it also offers significantly less control over when and how these external features are utilised.

## Examples

This repository contains some example binaries that can be built:

* `cmd/cat`
* `cmd/ls`
* `cmd/pwd`

You will notice there is a 4th directory called `wont_work`. This will not produce a binary as it does not satisfy the `The package is named 'main'` criteria.
