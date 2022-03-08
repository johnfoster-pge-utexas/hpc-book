---
jupytext:
  formats: ipynb,md:myst
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

# Plots.jl

+++ {"slideshow": {"slide_type": "slide"}}

## Install

[Plots.jl](https://docs.juliaplots.org/stable/) is not installed by default when Julia is installed.  To ensure it's installed, either run

```julia
using Pkg; Pkg.add("Plots")
```

from the Julia REPL or enter the package manager with `]` and then run

```julia
add Plots
```

+++ {"slideshow": {"slide_type": "slide"}}

## Basic Example

+++ {"slideshow": {"slide_type": "skip"}}

The easiest way to learn Plots.jl is with illustrative examples.  

```{code-cell}
---
slideshow:
  slide_type: '-'
---
using Plots

plot([0, 1, 2, 3])
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Changing the plot style 

+++ {"slideshow": {"slide_type": "skip"}}

There are many options for changing the plot style.  You have ultimate control over the entire look and feel.  

+++ {"slideshow": {"slide_type": "-"}}

The primary way to change the look of the plot is by specifying *attributes*.  A full list of plot attribues is available [here](https://docs.juliaplots.org/stable/attributes/).

```{code-cell}
---
slideshow:
  slide_type: '-'
---
plot([0, 1, 2, 3], xlabel="Some Numbers", grid=false)
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Adding multiple lines 

```{code-cell}
---
slideshow:
  slide_type: '-'
---
t = 0:0.2:5

plot(t, t, color=:blue, label="linear", xlab="t")
plot!(t, t .^ 2, color="black", label="quadratic")
plot!(t, t .^ 3, color="red", label="cubic")
```

+++ {"slideshow": {"slide_type": "slide"}}

## Themes 

+++ {"slideshow": {"slide_type": "-"}}

There are many built in plot *themes*.  A full list is available [here](https://docs.juliaplots.org/stable/generated/plotthemes/).

```{code-cell}
---
slideshow:
  slide_type: '-'
---
theme(:dark)

t = 0:0.2:5

plot(t, t, color=:blue, label="linear", xlab="t")
plot!(t, t .^ 2, color="black", label="quadratic")
plot!(t, t .^ 3, color="red", label="cubic")
```

+++ {"slideshow": {"slide_type": "fragment"}}

You can add attributes to the `theme` function and those will become the default settings for all plots that follow.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
theme(:dark, grid=:false)

t = 0:0.2:5

plot(t, t, color=:blue, label="linear", xlab="t")
plot!(t, t .^ 2, color="black", label="quadratic")
plot!(t, t .^ 3, color="red", label="cubic")
```

+++ {"slideshow": {"slide_type": "slide"}}

## Subplots / layouts

```{code-cell}
---
slideshow:
  slide_type: skip
tags: [output_scroll]
---
using CSV
using Tables

data = CSV.File("data/200wells.csv", header=false, skipto=2) |> Tables.matrix
permeability = data[:, 6]
porosity = data[:, 5];
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
theme(:default, label=nothing)


p1 = scatter(porosity, permeability, xlabel="porosity", ylabel="permeability")
p2 = histogram(porosity, title="Porosity")
p3 = histogram(permeability, title="Permeability")


l = @layout [
    a{0.6w} [b{0.5h}
             c{0.5h}  ]
]
plot(p1, p2, p3, layout=l)
```

+++ {"slideshow": {"slide_type": "slide"}}

## Contour plots 

+++ {"slideshow": {"slide_type": "skip"}}

The following is an example of a filled contour plot in Plots.jl using the command. This figure shows the depth of a petroleum reservoir.

Contour plots must have data that is defined on a rectangular grid in the $(x, y)$ plane.  In the example below, the file `nechelik.npy` has already been organized in this way.  Scattered data must be interpolated onto a rectangular grid.  Any data that has the format of a floating point `NaN` will be shown as white space in the contour plot.

```{code-cell}
---
slideshow:
  slide_type: skip
---
using NPZ

data = npzread("data/nechelik.npy")

X = data[1, :, :]
Y = data[2, :, :]
Z = data[3, :, :];
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
contourf(X[1, :], Y[:, 1], Z, linewidth=0, grid=false)
```

+++ {"slideshow": {"slide_type": "slide"}}

## Surface plots

+++ {"slideshow": {"slide_type": "skip"}}

The following example is a surface plot; however, the default *backend* [`GR`]([https://docs.juliaplots.org/stable/backends/#[GR]) does not interpolate the surface well.  It's better to use a different backend.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
plot(X, Y, Z, st=:surface, display_option=Plots.GR.OPTION_SHADED_MESH)
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Plotly backend

+++ {"slideshow": {"slide_type": "skip"}}

Not only does the Plotly backend interpolate the surface better, it also allows for interactive plots. More information and a full list of backends is available [here](https://docs.juliaplots.org/latest/backends/).

```{code-cell}
---
slideshow:
  slide_type: '-'
---
plotlyjs()
plot(X, Y, Z, st=:surface)
```

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
