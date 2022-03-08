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

+++ {"slideshow": {"slide_type": "skip"}}

# Functions

+++ {"slideshow": {"slide_type": "slide"}}

## Function definitions

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function f(x, y)
    x += 2
    x + y
end
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
f(x, y) = x + y
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
∑(x, y) = f(x, y)
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
∑(3, 4)
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Return statement

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function g(x, y)
    return x * y
    x + y
end
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
g(2, 3)
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Specifying Types

```{code-cell}
---
slideshow:
  slide_type: '-'
---
g(x::Integer, y::Integer)::Int8 = x + y
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
typeof(g(2,2))
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Return tuples

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function return_tuple(x, y)
    x + 2, y + 2
end
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
return_tuple(2, 4)
```

+++ {"slideshow": {"slide_type": "fragment"}}

### Unpacking

```{code-cell}
---
slideshow:
  slide_type: '-'
---
a, b = return_tuple(2, 4)
@show a, b;
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Optional Arguments

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function optional(x, y=1)
    x + y
end
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
optional(4)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
optional(4, 3)
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Keyword arguments

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function keywd(x; plus=2)
    x + plus
end
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
keywd(3, plus=3)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
keywd(3, 3)
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Anonymous functions

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function call_fn(f)
    f(3)
end
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
call_fn(x -> x ^ 2)
```

```{code-cell}
call_fn(x -> x + 4)
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
