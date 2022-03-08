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

# Types and type declarations

```{code-cell}
---
slideshow:
  slide_type: '-'
---
x = 100.0
typeof(x)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
function foo()
    x::Int8 = 100
    x
end

x = foo()
typeof(x)
```

+++ {"slideshow": {"slide_type": "slide"}}

## Type trees

```{code-cell}
---
slideshow:
  slide_type: skip
tags: [remove_cell]
---
using AbstractTrees
AbstractTrees.children(x::Type) = subtypes(x)
```

```{code-cell}
---
slideshow:
  slide_type: skip
tags: [remove_cell]
---
print_tree(Any)
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
print_tree(AbstractArray)
```

+++ {"slideshow": {"slide_type": "slide"}}

## Types in function arguments

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function g(x, y)
    return x * y
end

x = g(2, 3)
@show x
typeof(x)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
x = g(2.0, 3.0)
@show x
typeof(x)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
x = g("foo", "bar")
@show x
typeof(x)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
function f(x::Number, y::Number)
    return x * y
end

x = f(2, 3)
@show x
typeof(x)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
x = f("foo", "bar")
```

+++ {"slideshow": {"slide_type": "slide"}}

## Type Unions

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function h(x::Union{Integer, String}, y::Union{Integer, String})
    x * y
end

@show h(1, 2)
@show h("foo", "bar");
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
h(1.0, 2.0)
```

+++ {"slideshow": {"slide_type": "slide"}}

## Composite Types 

```{code-cell}
---
slideshow:
  slide_type: '-'
---
struct Point2D
    x::Float64
    y::Float64
end

p = Point2D(1.0, 2.0)
@show p.x
@show p.y;
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Parametric Composite Types

```{code-cell}
---
slideshow:
  slide_type: '-'
---
struct Point3D{T}
    x::T
    y::T
    z::T
end

p = Point3D{Int8}(1, 2, 3)
@show p.x
typeof(p.y)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
p = Point3D{Float32}(1.0, 2.0, 3.0)
typeof(p.x)
```

```{code-cell}
p = Point3D{String}("foo", "bar", "foobar")
typeof(p.x)
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Type restrictions on parameters

```{code-cell}
---
slideshow:
  slide_type: '-'
---
struct NumericPoint2D{T<:Real}
    x::T
    y::T
end 

p = NumericPoint2D{Float64}(1.0, 2.0)
@show typeof(p.x)

q = NumericPoint2D{Int8}(1, 2)
@show typeof(q.x)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
NumericPoint2D{String}("foo", "bar")
```

```{code-cell}
---
slideshow:
  slide_type: skip
---
import Base.+
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Functions and operators with parametric types

```{code-cell}
---
slideshow:
  slide_type: '-'
---
+(A::NumericPoint2D{T}, B::NumericPoint2D{T}) where T = NumericPoint2D{T}(A.x + B.x, A.y + B.y)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
A = NumericPoint2D{Int32}(1, 2)
B = NumericPoint2D{Int32}(1, 2)

A + B
```

+++ {"slideshow": {"slide_type": "fragment"}}

**Fields of composite types are immutable by default**

```{code-cell}
---
slideshow:
  slide_type: '-'
---
A.x = 2
```

+++ {"slideshow": {"slide_type": "slide"}}

## Mutable composite types

```{code-cell}
---
slideshow:
  slide_type: '-'
---
mutable struct MutablePoint2D{T<:Real}
    x::T
    y::T
end 

A = MutablePoint2D{Int32}(1, 2)
A.x = 2
@show A.x;
```

+++ {"slideshow": {"slide_type": "slide"}}

## Operations on types

```{code-cell}
---
slideshow:
  slide_type: '-'
---
isa(1, Int)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
typeof(1)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
supertype(UInt32)
```

+++ {"slideshow": {"slide_type": "slide"}}

## Constructors 

```{code-cell}
---
slideshow:
  slide_type: '-'
---
struct Polar{T<:Real}
    r::T
    θ::T
end

A = Polar(1.0, π / 4)
A.θ
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
function Polar(A::NumericPoint2D{T}) where T
    r = sqrt(A.x ^ 2 + A.y ^ 2)
    θ = atan(A.y, A.x)
    Polar{T}(r, θ)
end
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
A = NumericPoint2D{Float32}(cos(π / 4), sin(π / 4))
B = Polar(A)
@show B.r
@show B.θ
typeof(B.r)
```

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
