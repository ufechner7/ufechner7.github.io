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

It is a good habit to return `nothing`, unless you want to return your result, e.g. a plot (diagram) or a dataset.

## Scripts that are using packages
If you are using any packages, you should create a proper project to keep track of your dependencies and their versions.

That is simple:
Creating a project does not mean to create a package. It is much simpler:
```bash
mkdir test
cd test
mkdir src
julia --project="."
```
Now add the packages you need:
```julia
using Pkg
pkg"add Plots"
pkg"add DataFrames"
```
Now put your code in a file in the src folder, for example like this:
```bash
cd src
gedit my_plot.jl
```
and put the following code into it:
```julia
using Plots, DataFrames

time = 0:0.01:10             # step range from 0 to 10 step 0.1
u    = sin.(time*5)          # signal with a frequency of 5 rad/s
step = 1 .- 1 ./ exp.(time)  # step response
df = DataFrame(;time, u, step)
plot(df.time,  u, legend=false)
plot!(df.time, step)
```
and save it.
If you want to run it, make sure you are in the `test` folder and then
start julia with:
```bash
julia --project
```
and execute your script with:
```julia
include("src/my_plot.jl")
```
You should see the following plot:
![myplot](myplot.png)

When you are happy with your code and the packages you are using, make a backup copy
of your Manifest.toml file:
```bash
cp Manifest.toml Manifest.toml.bak
```
If you - half a year later - update your packages and your code stops to work, just restore the Manifest file:
```bash
cp Manifest.toml.bak Manifest.toml
```
No need to create any module or Julia package...

