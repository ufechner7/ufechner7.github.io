---
title: "Filtering and browsning datasets"
date: 2022-08-13
tags: Julia 
published: false
---
# Filtering and browsing datasets

## Introduction
The basic work flow of data analysis is filtering a large data set to extract the interesting
aspects and to browse and/or plot the result. In this post I will give a basic example for
filtering, browsing and plotting of data.

## Creating a test project
For trying out a new package and/or example it is always good to create a new project first.
*This is not needed when you followed my first post.*

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
julia> pkg"add Printf"
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
all the messages sent to the output devices. 
