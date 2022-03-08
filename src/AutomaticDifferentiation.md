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

# Automatic Differentiation

+++ {"slideshow": {"slide_type": "slide"}}

## Recall the chain rule

Any computer program can be thought of as a composition of functions, i.e.

$$
output = f(g(h(input))) = f(g(h(w_0)) = f(g(w_1)) = f(w_2) = w_3
$$

where

\begin{align*}
w_0 &= input \\
w_1 &= h(w_0) \\
w_2 &= g(w_1) \\
w_3 &= f(w_2) = output
\end{align*}

If we want to compute

$$
\frac{d}{d(input)}(output)
$$

we can use the chain rule, i.e.

$$
\frac{d}{d(input)}(output) = \frac{d(output)}{dw_2}\frac{dw_2}{dw_1}\frac{dw_1}{d(input)}
$$


The computer can do this for us!  See [here](https://en.wikipedia.org/wiki/Automatic_differentiation) for more details.

+++ {"slideshow": {"slide_type": "slide"}}

## Forward vs. Reverse Mode AD

 * **Forward AD** traverses the chain rule from inside to outside
     * Easy to implement
     * Lower memory footprint
     
     
 * **Reverse AD** traverses the chain rule from outside to inside
     * Requires half the operations of Forward AD
     * More difficult to implement 
     * Larger memory footprint

+++ {"slideshow": {"slide_type": "slide"}}

## ForwardDiff.jl 

Forward mode AD in Julia

Let's start with the example $f(x) = 3 x^2$ which of course has the analytic derivative

$$
f'(x) = 6 x
$$

```{code-cell}
using ForwardDiff

f(x) = 3*x^2

ForwardDiff.derivative(f, 3)
```

+++ {"slideshow": {"slide_type": "subslide"}}

## In place functions

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function f!(y, x)
  y[1] = 3.0 * x^2
  y[2] = x^4
  nothing
end

y = [0.0, 0.0]
f!(y, 3)
@show y;
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
y = [0.0, 0.0]
ForwardDiff.derivative(f!, y, 3)
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
y = [0.0, 0.0]
dy = [0.0, 0.0]
ForwardDiff.derivative!(dy, f!, y, 3)
@show dy;
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Gradients

Recall the gradient of a scalar function is

$$
\nabla f(x_1, x_2) = \left[ \frac{\partial f}{\partial x_1}, \frac{\partial f}{\partial x_2}\right]
$$

```{code-cell}
---
slideshow:
  slide_type: '-'
---
g(x) = 3.0 * x[1]^2 + x[2]^4
g([1.0, 2.0])
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
ForwardDiff.gradient(g, [1.0, 2.0])
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
h(x, p) = p[1] * x[1]^2 + p[2] * x[2]^4

h([1.0, 2.0], [3.0, 1.0])
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
ForwardDiff.gradient(x -> h(x, [3.0, 1.0]), [1.0, 2.0])
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Jacobian

Recall the Jacobian of a vector function is

$$
\nabla \vec{f}(x_1, x_2) = 
\begin{bmatrix}
\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\
\frac{\partial f_2}{\partial x_1} & \frac{\partial f_2}{\partial x_2}
\end{bmatrix}
$$

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function f2(x)
  [3.0 * x[1]^2, x[1] * x[2]^4]
end

f2([3.0, 1.0])
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
ForwardDiff.jacobian(f2, [3.0, 1.0])
```

+++ {"slideshow": {"slide_type": "slide"}}

### Other AD packages in Julia

 * [Zygote.jl](https://fluxml.ai/Zygote.jl/latest/) - Reverse Mode AD, source-to-source
 
 * [Diffractor.jl](https://github.com/JuliaDiff/Diffractor.jl) - Next generation AD package w/ API similar to Zygote

```{code-cell}
---
hide_input: true
init_cell: true
javascript_last_cell: true
jupyter:
  source_hidden: true
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
