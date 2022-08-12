---
title: "Filtering and browsning datasets"
date: 2022-08-13
tags: Julia InMemoryDatasets
published: true
---
# Filtering and browsing datasets

## Introduction
The basic work flow of data analysis is filtering a large data set to extract the interesting
aspects and to browse and/or plot the result. In this post I will give a basic example for
filtering and browsing of data, plotting deserves a blog post on its own.

## Creating a test project
For trying out a new package and/or example it is always good to create a new project first.
*When you followed my first post you only have to add the package TerminalPager*.

For example using the following commands:
```bash
mkdir can
cd can
julia --project="."
```
And then install the required packages:
```julia
julia> using pkg
julia> pkg"add InMemoryDatasets"
julia> pkg"add TerminalPager"
```
Now quit julia with <CTRL><D> and restart it with:
```bash
julia --project -t auto
```
This uses the set of packages we just installed and starts julia using all available threads. This is useful when handling large data sets (millions of messages).

## Creating a sample data set
To get a realistic impression of the performance we now create a sample data set with 100k entries. We use again a CAN bus log file as example, this time with m=8 data columns, one byte per column. For the address column, which uses two bytes we use a range of 46 different, randomly choosen values, starting with 0x101.

```julia
using InMemoryDatasets, DLMReader, Printf

function demo_data()
    n = 100000
    m = 8
    time = 0.1:0.1:n*0.1
    addr = rand(Int16(0x101):Int16(0x12e), n)
    data = (Symbol("d", i) => rand(UInt8, n) for i in 1:m)
    ds = Dataset(;time, addr, data...)
    ds.addr[5] = missing
    ds
end
ds = demo_data()
```
If we now print the data set in the julia console, only the first and the last values are shown:
```
julia> ds = demo_data()
100000×10 Dataset
    Row │ time      addr      d1        d2        d3        d4        d5        d6        d7        d8       
        │ identity  identity  identity  identity  identity  identity  identity  identity  identity  identity 
        │ Float64?  Int16?    UInt8?    UInt8?    UInt8?    UInt8?    UInt8?    UInt8?    UInt8?    UInt8?   
────────┼────────────────────────────────────────────────────────────────────────────────────────────────────
      1 │      0.1       264       127         5       247       185       212       216       171       147
      2 │      0.2       278       127       239       183        48       127        98        27        44
      3 │      0.3       301         6        16       159       201       225        95       196        51
   ⋮    │    ⋮         ⋮         ⋮         ⋮         ⋮         ⋮         ⋮         ⋮         ⋮         ⋮
  99999 │   9999.9       294        95       185        15       205       178        64        13        31
 100000 │  10000.0       285        86       224        79       214       248        39        34       147
                                                                                           99995 rows omitted
```
## Formating the data
For CAN bus messages usually the hexadecimal data format is used. To see the numbers in hex format we need to define two functions, one that is formating the data bytes with two hex digits, called `hex2(n)` and one that formats the address column with four hex digits, called `hex4(n)`. To save space we want to display missing values with the string "--".
```julia
function hex2(n)
    if ismissing(n) return "--" end
    string(n, base=16, pad=2)
end

function hex4(n)
    if ismissing(n) return "--" end
    string(n, base=16, pad=4)
end
```
The `setformat!` function can be used to set the format of a column of a dataset. The exclamation mark indicates that this function modifies the first argument. The function `Symbol` can be used to create symbols, composed of a letter and a number to be used in a loop.
```julia
setformat!(ds, :addr => hex4)
for i in 1:8
    setformat!(ds, Symbol("d", i) => hex2)
end
```

## Filtering the data
Let us assume that the devices with the address 0x103, 0x106 and 0x109 are output devices, and we want to filter
all the messages sent to the output devices. For complex filter conditions a function that returns `true` or `false` is needed. Missing values must be handled correctly.
```julia
# CAN bus devices with a given set of addresses are output devices
function isoutput(addr)
    if ismissing(addr) return false end
    addr in [0x103, 0x106, 0x109]
end
```
Finally we can apply the `filter` function, which requires three parameters, the input dataset, the column for applying the filter function and - as named parameter - the filter function.
```julia
# create the dataset msg_out with all messages sent to output devices
msg_out = filter(ds, :addr, by=isoutput)
```

## Browsing through the output data
The filtered data set `msg_out` is still too big to print it on one screen, so we need a way to browse through the result set. I am using the following function to do that:
```julia
function browse(ds)
    io = IOContext(IOBuffer(), :color => true);
    show(io, ds, show_row_number=false, eltypes=false)
    pager(String(take!(io.io)), frozen_rows=3)
end
```
The option `:color => true` allows the use of formatting, e.g. the header will be in bold.
The options `show_row_number=false` and `eltypes=false` suppress the row numbers and the row with the column types.

You can now browse through the output messages with the command:
```julia
browse(msg_out)
```
It will look like this (well, with the header in bold):
```
6475×10 Dataset
 time    addr  d1  d2  d3  d4  d5  d6  d7  d8 
──────────────────────────────────────────────
    8.2  0109  17  d6  13  e2  60  14  a4  8f
    9.9  0103  45  14  61  a3  6f  18  e7  35
   12.1  0109  7b  16  43  65  e9  f2  87  99
   12.8  0106  87  c0  46  f1  4e  49  ad  74
   13.8  0106  bc  88  f1  0a  a9  c4  99  ef
   14.1  0106  63  bb  83  96  8f  56  9a  99
   14.8  0106  f7  94  2b  01  b3  45  12  3a
   15.3  0109  3e  74  04  16  15  93  5f  0c
   15.6  0103  7b  e6  53  c7  0d  89  6b  f3
   15.7  0109  8e  db  d2  30  c1  b0  32  fa
   16.0  0106  5e  52  da  97  74  81  e6  b8
:  
```
You can scroll through the dataset with the cursor keys and the page up and page down keys. You can also search for a string with the slash key, then typing the search term and then \<ENTER\>. To finish browsing press `q`.

As you can see, all the data is nicely formatted as hexadecimal numbers.

## Further reading
To learn more about the filtering options, have a look at [Filter observations](https://sl-solution.github.io/InMemoryDatasets.jl/stable/man/filter/).
