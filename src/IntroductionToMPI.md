---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

+++ {"slideshow": {"slide_type": "slide"}}

# Introduction to Message Passing Interface

+++ {"slideshow": {"slide_type": "slide"}}

# What is MPI?

* Message Passing Interface
  * Most useful on distributed memory machines
  * The *de facto standard* parallel programming interface
  
* Many implementations, interfaces in C, Fortran, Python via MPI4Py, Julia via MPI.jl

+++ {"slideshow": {"slide_type": "subslide"}}

# Message Passing Paradigm

* A parallel MPI program is launched as separate processes (tasks), each with thier own address space.
  * Requires partitioning data across tasks.
* **Data is explicitly** moved from task to task 
  * A task accesses the data of another task through a transaction called "message passing" in which a copy of the
data (message) is transferred (passed) from one task to another.
* There are two classes of message passing
  * **Point-to-point** involve only two tasks
  * **Collective messages** involve a set of tasks

+++ {"slideshow": {"slide_type": "slide"}}

# MPI.jl

 * [MPI.jl](https://juliaparallel.github.io/MPI.jl/stable/) provides an interface very similar to the MPI standard C interface
 * You can communicate Julia objects.

+++ {"slideshow": {"slide_type": "fragment"}}

## Install

[MPI.jl](https://juliaparallel.github.io/MPI.jl/stable/) is not installed by default when Julia is installed.  To ensure it's installed, either run

```julia
using Pkg; Pkg.add("MPI")
```

from the Julia REPL or enter the package manager with `]` and then run

```julia
add MPI
```

this will install a set MPI binaries by default.  It's usually a good idea to
run the following commands from the Julia REPL once after installing MPI

```julia
using MPI; MPI.install_mpiexecjl()
```

which will install a *project aware* version of `mpiexec` for use with Julia
projects.  This is usually installed at `$HOME/.julia/bin`.

+++ {"slideshow": {"slide_type": "slide"}}

# Communicators

 * MPI uses communicator objects to identify a set of processes which communicate only within their set.
 * `MPI.COMM_WORLD` is defined as all processes (ranks) of your job.
 * Usually required for most MPI calls 
 * Rank
   * Unique process ID within a communicator
   * Assigned by the system when the process initalizes
   * Used to specify sources and destinations of messages

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: skip
tags: [remove_cell]
---
%%file hello.jl
#!/usr/bin/env julia
using MPI
MPI.Init()

comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

print("Hello world, I am rank $(rank) of $(size)\n")
```

+++ {"hide_input": false, "slideshow": {"slide_type": "fragment"}}

```julia
using MPI
MPI.Init()

comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

print("Hello world, I am rank $(rank) of $(size)\n")
```

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: fragment
---
!$HOME/.julia/bin/mpiexecjl --project=.. -np 4 julia hello.jl
```

```{code-cell} ipython3
---
hide_input: true
init_cell: true
slideshow:
  slide_type: skip
tags: [remove_cell]
---
%%javascript
function hideElements(elements, start) {
    for(var i = 0, length = elements.length; i < length;i++) {
        if(i >= start) {
            elements[i].style.display = "none";
        }
    }
}

var prompt_elements = document.getElementsByClassName("prompt");
hideElements(prompt_elements, 0)
```
