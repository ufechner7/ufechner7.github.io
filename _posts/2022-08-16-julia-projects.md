---
title: "Why am I using Julia?"
date: 2022-08-15
tags: Julia
published: false
---
# Working with projects
## Introduction
When you start to use Julia you might ask yourself: How shall I structure my code?
There are different approaches for each programming language.

## Simple scripts
If you just write short, simple scripts that are not using any packages you can just keep them in one file. If you care about performance you should put everything in a
function. Example:
```julia
# constants
const LANG="DE"

# functions
function hello(name)
    if LANG == "DE"
        println("Hallo liebe/r $name !")
    else
        println("Hello dear $name !")
    end
end

# main program
function main()
    hello("Peter")
    hello("Jane")
end

main()
nothing
```
The structure is:
- constants
- functions
- main function
- call the main function
- return nothing

Do not use global variables! That kills your performance.

If you store this code in a file with the name `hello.jl` you can execute it from the REPL with the command:
```julia
include("hello.jl")
```

The advantage of having a main() function is that you can include some error checks and return an error code if they fail. Furthermore you can measure the performance by running:
```julia
@time("hello.jl")
@time main()
```
The first timing you get includes the compilation time, the second number shows the pure execution time.

## Scripts that are using packages
If you are using any packages, you should create a proper project to keep track of your dependencies and their versions.

