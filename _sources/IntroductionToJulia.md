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

```{image} images/julia-logo.png
:alt: julia-logo
:width: 300px
:align: center
```

+++ {"slideshow": {"slide_type": "slide"}}

# Why Julia?

 * Fast
 
 * Dynamic, but... optionally typed
 
 * Composable (mulitple dispatch)
  
 * General Purpose
 
 * Open Source
 
 * Amazing Packages (e.g. DifferentialEquations.jl, Flux.jl)
 
 * Growing community ([Slack Workspace](https://join.slack.com/t/julialang/shared_invite/zt-w0pifg7p-18IUSkZy_WpofNumiTTROQ))

+++ {"slideshow": {"slide_type": "subslide"}}

# How fast?

```{image} images/benchmarks.png
:alt: benchmarks
:width: 600px
:align: center
```

+++ {"slideshow": {"slide_type": "subslide"}}

# Multiple Dispatch

```{code-cell}
---
slideshow:
  slide_type: '-'
---
my_add(x::Int, y::Int) = x + y
my_add(x::Float64, y::Float64) = x + y
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
my_add(1, 2)
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
my_add(1.0, 2)
```

+++ {"slideshow": {"slide_type": "fragment"}}

---

```{code-cell}
---
slideshow:
  slide_type: '-'
---
my_add(x::String, y::String) = "$(x) $(y)"
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
my_add("Hello", "World!")
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
import Base.+
+(x::String, y::String) = my_add(x, y)
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
"Hello" + "World!"
```

+++ {"slideshow": {"slide_type": "fragment"}}

---

```{code-cell}
---
slideshow:
  slide_type: '-'
---
struct Point
    x::Float64
    y::Float64
end

my_add(A::Point, B::Point) = Point(A.x + B.x, A.y + B.y)
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
A = Point(1.0, 2.0)
B = Point(1.0, 2.0)
my_add(A,B)
```

+++ {"slideshow": {"slide_type": "subslide"}}

# Generic programming 

```{code-cell}
---
slideshow:
  slide_type: '-'
---
using Measurements

x = 5.23 ± 0.14
y = 45.77 ± 0.1

x + y
```

+++ {"slideshow": {"slide_type": "fragment"}}

## Simple pendulum

$$
\ddot{\theta} = \frac{g}{L} \theta
$$

[Example Source](https://tutorials.sciml.ai/html/type_handling/02-uncertainties.html)

```{code-cell}
---
slideshow:
  slide_type: '-'
---
using DifferentialEquations, Measurements, Plots

g = 9.79 ± 0.02; # Gravitational constants
L = 1.00 ± 0.01; # Length of the pendulum

#Initial Conditions
u₀ = [0 ± 0, π / 60 ± 0.01] # Initial speed and initial angle
tspan = (0.0, 6.3)

#Define the problem
function simplependulum(du,u,p,t)
    θ  = u[1]
    dθ = u[2]
    du[1] = dθ
    du[2] = -(g/L)*θ
end

#Pass to solvers
prob = ODEProblem(simplependulum, u₀, tspan)
sol = solve(prob, Tsit5(), reltol = 1e-6)

# Analytic solution
u = u₀[2] .* cos.(sqrt(g / L) .* sol.t)

plot(sol.t, getindex.(sol.u, 2), label = "Numerical")
plot!(sol.t, u, label = "Analytic")
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
