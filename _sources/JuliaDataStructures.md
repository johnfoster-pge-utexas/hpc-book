---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Julia 1.7.2
  language: julia
  name: julia-1.7
---

+++ {"slideshow": {"slide_type": "slide"}}

# Common Data Structures

+++ {"slideshow": {"slide_type": "slide"}}

## Arrays


Array's are N-dimensional data structures of a common type (the type could be `Any`).  The type signature is

```julia
Array{T, N}
```

where `T` is the type and `N` is the dimension.  You can omit `N` and it will be inferred for the arguments

```{code-cell}
---
slideshow:
  slide_type: '-'
---
Array{Any, 3}(nothing, 3, 2, 3)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
x = Array{Any}(nothing, 2, 3)
```

+++ {"slideshow": {"slide_type": "fragment"}}

A `Vector` is an `Array` with `N=1`, a `Matrix` is an `Array` with `N=2`.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
Vector{Float64}(undef, 3)
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
Matrix{Float64}(undef, 3, 3)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
Float64[1.0, 2.0, 3.0]
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
y = Float64[1.0 2.0 3.0; 4.0 5.0 6.0]
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Array indexing and assignment

```{code-cell}
---
slideshow:
  slide_type: '-'
---
y[1, 2]
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
y[1:2, 2]
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
y[end,end] = 100;
y
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
x[1, 1] = "test"
x[2, 1:3] .= 1
x
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Views

```{code-cell}
---
slideshow:
  slide_type: '-'
---
z = y[1:2, 1:2]
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
z[1,1] = 100.0
@show z
@show y;
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
z = @view y[1:2, 1:2]
z[1, 1] = 100.0
y
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Convenience constructors

```{code-cell}
---
slideshow:
  slide_type: '-'
---
zeros(Int32, 2, 3)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
zeros(2, 3)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
ones(2, 3)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
fill(77.0, 2, 2)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
fill!(y, 22.0)
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Operations on arrays

Multiplication of a `Matrix` and a `Vector` automatically does matrix multiplication.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
@show y
@show x = [1.0, 2.0, 3.0]
y * x
```

+++ {"slideshow": {"slide_type": "fragment"}}

If we want element-wise multiplication we need to use `.*`.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
x .* x
```

+++ {"slideshow": {"slide_type": "fragment"}}

Addition/subtraction of two `Array`s of the same size gives element-wise addition.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
x + x
```

+++ {"slideshow": {"slide_type": "fragment"}}

We can *broadcast* the addition operation along matching dimensions of `Array`s.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
@show size(y')
@show size(x)

y' .+ x
```

+++ {"slideshow": {"slide_type": "fragment"}}

Any scalar operation can be *vectorized* with the `.` operator, i.e.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
my_scalar_operation(x, y) = x * y + x^2 * y^2

my_scalar_operation(1.0, 2.0)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
my_scalar_operation.([1.0, 2.0, 3.0], [4.0, 5.0, 6.0])
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Array functions

There are numerous array functions such as `sum`, `cumsum`, `prod`, etc.  For example

```{code-cell}
---
slideshow:
  slide_type: '-'
---
accumulate(+, [1, 2, 3])
```

+++ {"slideshow": {"slide_type": "-"}}

See [Array documentation](https://docs.julialang.org/en/v1.7/base/arrays/#Array-functions) for more examples.

+++ {"slideshow": {"slide_type": "slide"}}

## Tuples

Tuples are fixed length containers that can hold any values, but cannot be modified (they are *immutable*).

```{code-cell}
---
slideshow:
  slide_type: '-'
---
x = ("a string", 1.0, 33)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
x[1]
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
x[2] = 2.0 
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Named Tuples

```{code-cell}
---
slideshow:
  slide_type: '-'
---
x = (a = "a string", b = 1.0, c = 33) 
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
@show x[1]
@show x.a;
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
_, bb, _ = x
@show bb;
```

+++ {"slideshow": {"slide_type": "slide"}}

## Dictionaries

Dictionaries are data structures to store and lookup key-value pairs, i.e.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
dict = Dict("a" => "a string", "b" => 33, "name" => "John")

@show dict["b"]
@show dict["name"];
```

+++ {"slideshow": {"slide_type": "fragment"}}

They can also be constructed using a collection of tuples.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
dict = Dict([("a","a string"), ("b", 33), ("name", "John")])

@show dict["b"]
@show dict["name"];
```

+++ {"slideshow": {"slide_type": "slide"}}

## More Data Structures 

More information of these data structures as well as many others that Julia offers can be found in the documentation [here](https://docs.julialang.org/en/v1.7/base/collections/)

```{code-cell}
---
hide_input: true
init_cell: true
slideshow:
  slide_type: skip
tags: [remove_cell]
---
macro javascript_str(s) display("text/javascript", s); end
javascript"""
function hideElements(elements, start) {
for(var i = 0, length = elements.length; i < length;i++) {
    if(i >= start) {
        elements[i].style.display = "none";
    }
}
}
var prompt_elements = document.getElementsByClassName("prompt");
hideElements(prompt_elements, 0)
"""
```
