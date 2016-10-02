# PairwiseListMatrices

Julia 0.4: [![PairwiseListMatrices](http://pkg.julialang.org/badges/PairwiseListMatrices_0.4.svg)](http://pkg.julialang.org/?pkg=PairwiseListMatrices)
Julia 0.5: [![PairwiseListMatrices](http://pkg.julialang.org/badges/PairwiseListMatrices_0.5.svg)](http://pkg.julialang.org/?pkg=PairwiseListMatrices)
Linux, OSX: [![Build Status](https://travis-ci.org/diegozea/PairwiseListMatrices.jl.svg?branch=master)](https://travis-ci.org/diegozea/PairwiseListMatrices.jl)
Windows: [![Build status](https://ci.appveyor.com/api/projects/status/p96sso5b23gi85mg/branch/master?svg=true)](https://ci.appveyor.com/project/diegozea/pairwiselistmatrices-jl/branch/master)
Code Coverage: [![Coverage Status](https://coveralls.io/repos/diegozea/PairwiseListMatrices.jl/badge.svg?branch=master&service=github)](https://coveralls.io/github/diegozea/PairwiseListMatrices.jl?branch=master) [![codecov.io](http://codecov.io/github/diegozea/PairwiseListMatrices.jl/coverage.svg?branch=master)](http://codecov.io/github/diegozea/PairwiseListMatrices.jl?branch=master)

**Documentation:** [http://diegozea.github.io/PairwiseListMatrices.jl/](http://diegozea.github.io/PairwiseListMatrices.jl/)

## Description

This package allows you to use a pairwise list as a matrix:

![PLM](https://raw.githubusercontent.com/diegozea/PairwiseListMatrices.jl/master/doc/PLM_README.png)

```julia
type PairwiseListMatrix{T, diagonal} <: AbstractArray{T, 2}
  list::AbstractVector{T}
  diag::AbstractVector{T}
  labels::IndexedArray
  nelements::Int
end
```
`PairwiseListMatrix{T, diagonal}` is a (squared) symmetric matrix that stores a `list` of values of type `T` for the pairwise comparison/measure of `nelements`.
If the parameter `diagonal` is `true` the first element of the list is `1, 1`, otherwise is `1, 2`. The diagonal values are stored in a vector on the `diag` field when `diagonal` is `false`.
Labels can be stored on the field `labels` as an [IndexedArray](https://github.com/garrison/IndexedArrays.jl).

## Features

#### Labels
`PairwiseListMatrix` gives the option of save labels and allows to use them for indexing.

#### Space
In pairwise calculations like `cor()` if results are saved as `PairwiseListMatrix` the space is `N(N+1)/2` instead of `N*N`. This is useful to compare a large number of elements, because you are **saving ~ 50% of the memory.**

#### Time
`PairwiseListMatrix` is **faster than a full matrix** to make operatation like `sum` and `mean` in the whole matrix, since it is cache efficient. However it is slower than a full matrix for reducing along dimensions.

 - [Creation benchmark](http://nbviewer.ipython.org/github/diegozea/PairwiseListMatrices.jl/blob/master/test/creation_bech.ipynb)
 - [Statistics benchmark](http://nbviewer.ipython.org/github/diegozea/PairwiseListMatrices.jl/blob/master/test/stats_bench.ipynb)

#### Plots
Since this could be a representation for an **adjacency matrix/list** of an undirected graph, the function `protovis` provides an **arc diagram** and a **matrix visualization** on the web browser using [Protovis](http://mbostock.github.io/protovis/).

![Protovis example](https://raw.githubusercontent.com/diegozea/PairwiseListMatrices.jl/master/doc/protovis_example.png)

## Example

```julia
julia> # Pkg.add("PairwiseListMatrices")

julia> using PairwiseListMatrices

shell>  cat example.csv
A,B,10
A,C,20
B,C,30

julia> data = readcsv("example.csv")
3x3 Array{Any,2}:
 "A"  "B"  10
 "A"  "C"  20
 "B"  "C"  30

julia> list = from_table(data, Int, false)
3x3 PairwiseListMatrices.PairwiseListMatrix{Int64,false}:
  0  10  20
 10   0  30
 20  30   0

julia> list.list
3-element Array{Int64,1}:
 10
 20
 30

julia> labels(list)
3-element IndexedArrays.IndexedArray{Any}:
 "A"
 "B"
 "C"

julia> getlabel(list, "A", "B")
10

julia> list[1,2]
10

julia> full(list)
3x3 Array{Int64,2}:
  0  10  20
 10   0  30
 20  30   0

```
