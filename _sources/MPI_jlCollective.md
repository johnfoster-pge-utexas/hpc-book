---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.13.7
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

+++ {"slideshow": {"slide_type": "slide"}}

# Collective Communication


<table>
    <tr style="background-color:transparent">
        <td><img src="images/collective_comm.gif" width=400/></td>
    </tr>
</table>

[Image Source](https://computing.llnl.gov/tutorials/parallel_comp/images/collective_comm.gif)

+++ {"slideshow": {"slide_type": "slide"}}

# Scatter

Rank 0 acts as a leader, creating a list and scattering it out to all
ranks evenly

+++ {"hide_input": false, "slideshow": {"slide_type": "-"}}

```julia
using MPI

MPI.Init()
comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

send_buf = nothing
recv_buf = Vector{Float64}(undef, size)

if rank == 0
    send_buf = collect(1:size) .* 100
    print("Original array on rank 0:\n $(send_buf)\n")
end
    
recv_buf = MPI.Scatter(send_buf, Int, comm; root=0)
print("I got this on rank $(rank):\n $(recv_buf)\n")
```

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: skip
tags: [remove_cell]
---
%%file Scatter1.jl
#!/usr/bin/env julia
using MPI

MPI.Init()
comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

send_buf = nothing
recv_buf = Vector{Float64}(undef, size)

if rank == 0
    send_buf = collect(1:size) .* 100
    print("Original array on rank 0:\n $(send_buf)\n")
end
    
recv_buf = MPI.Scatter(send_buf, Int, comm; root=0)
print("I got this on rank $(rank):\n $(recv_buf)\n")
```

+++ {"slideshow": {"slide_type": "fragment"}}

If the preceding code is saved to a file `Scatter1.jl`, we can run

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: '-'
---
!~/.julia/bin/mpiexecjl --project=.. -np 2 julia Scatter1.jl
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Scatter!

+++ {"hide_input": false, "slideshow": {"slide_type": "-"}}

```julia
using MPI

MPI.Init()
comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

send_buf = nothing
recv_buf = Vector{Float64}(undef, size)

if rank == 0
    send_buf = rand(Float64, (size, size))
    print("Original array on rank 0:\n $(send_buf)\n")
end
    
MPI.Scatter!(send_buf, recv_buf, comm; root=0)
print("I got this array on $(rank):\n $(recv_buf)\n")
```

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: skip
tags: [remove_cell]
---
%%file Scatter2.jl
#!/usr/bin/env julia
using MPI

MPI.Init()
comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

send_buf = nothing
recv_buf = Vector{Float64}(undef, size)

if rank == 0
    send_buf = rand(Float64, (size, size))
    print("Original array on rank 0:\n $(send_buf)\n")
end
    
MPI.Scatter!(send_buf, recv_buf, comm; root=0)
print("I got this array on $(rank):\n $(recv_buf)\n")
```

+++ {"slideshow": {"slide_type": "fragment"}}

If the preceding code is saved to a file `Scatter2.jl`, we can run

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: '-'
---
!~/.julia/bin/mpiexecjl --project=.. -np 2 julia Scatter2.jl
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Scatterv!

+++ {"hide_input": false, "slideshow": {"slide_type": "-"}}

```julia
using MPI

MPI.Init()
comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

send_buf = nothing

if rank == 0
    recv_buf = Vector{Float64}(undef, size-1)
    send_buf = rand(Float64, (size, size))
    print("Original array on rank 0:\n $(send_buf)\n")
else
    recv_buf = Vector{Float64}(undef, size+1)
end

lengths = [size-1, size+1]
offsets = [0, size-1]
    
MPI.Scatterv!(VBuffer(send_buf, lengths, offsets, MPI.DOUBLE), recv_buf, comm; root=0)
print("I got this array on $(rank):\n $(recv_buf)\n")
```

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: skip
tags: [remove_cell]
---
%%file Scatter3.jl
#!/usr/bin/env julia
using MPI

MPI.Init()
comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

send_buf = nothing

if rank == 0
    recv_buf = Vector{Float64}(undef, size-1)
    send_buf = rand(Float64, (size, size))
    print("Original array on rank 0:\n $(send_buf)\n")
else
    recv_buf = Vector{Float64}(undef, size+1)
end

lengths = [size-1, size+1]
offsets = [0, size-1]
    
MPI.Scatterv!(VBuffer(send_buf, lengths, offsets, MPI.DOUBLE), recv_buf, comm; root=0)
print("I got this array on $(rank):\n $(recv_buf)\n")
```

+++ {"slideshow": {"slide_type": "fragment"}}

If the preceding code is saved to a file `Scatter3.jl`, we can run

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: '-'
---
!~/.julia/bin/mpiexecjl --project=.. -np 2 julia Scatter3.jl
```

+++ {"slideshow": {"slide_type": "slide"}}

# Gather

`Gather` is a command that collects results from all processes into a list.

+++ {"hide_input": false, "slideshow": {"slide_type": "-"}}

```julia
using MPI

MPI.Init()
comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

send_buf = nothing

if rank == 0
    send_buf = collect(1:size) .* 100
    print("Original array on rank 0:\n $(send_buf)\n" )
end
    
v = MPI.Scatter(send_buf, Int, comm; root=0)
print("I got this on $(rank):\n $(v)\n")

v = v * v

recv_buf = MPI.Gather(v, comm; root=0)
if rank == 0
    print("New array on rank 0:\n $(recv_buf)")
end
```

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: skip
tags: [remove_cell]
---
%%file Gather.jl
#!/usr/bin/env julia
using MPI

MPI.Init()
comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

send_buf = nothing

if rank == 0
    send_buf = collect(1:size) .* 100
    print("Original array on rank 0:\n $(send_buf)\n" )
end
    
v = MPI.Scatter(send_buf, Int, comm; root=0)
print("I got this on $(rank):\n $(v)\n")

v = v * v

recv_buf = MPI.Gather(v, comm; root=0)
if rank == 0
    print("New array on rank 0:\n $(recv_buf)")
end
```

+++ {"slideshow": {"slide_type": "fragment"}}

If the preceding code is saved to a file `Gather.jl`, we can run

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: '-'
---
!~/.julia/bin/mpiexecjl --project=.. -np 2 julia Gather.jl
```

+++ {"slideshow": {"slide_type": "fragment"}}

See also [`Gather!`](https://juliaparallel.github.io/MPI.jl/latest/collective/#MPI.Gather!), [`Gatherv!`](https://juliaparallel.github.io/MPI.jl/latest/collective/#MPI.Gatherv!), and [`Allgather`](https://juliaparallel.github.io/MPI.jl/latest/collective/#MPI.Allgather)/[`Allgather!`](https://juliaparallel.github.io/MPI.jl/latest/collective/#MPI.Allgather!)/[`Allgatherv!`](https://juliaparallel.github.io/MPI.jl/latest/collective/#MPI.Allgatherv!)

+++ {"slideshow": {"slide_type": "slide"}}

# Broadcast

`bcast` sends a single object to every process.

+++ {"hide_input": false, "slideshow": {"slide_type": "-"}}

```julia
using MPI

MPI.Init()

comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

send_buf = nothing

if rank == 0
    send_buf = "Hello from rank 0!\n"
end

recv_buf = MPI.bcast(send_buf, comm; root=0)
print("Rank $(rank) recieved this message:\n  $(recv_buf)\n")
```

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: skip
tags: [remove_cell]
---
%%file broadcast.jl
#!/usr/bin/env julia
using MPI

MPI.Init()

comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

send_buf = nothing

if rank == 0
    send_buf = "Hello from rank 0!\n"
end

recv_buf = MPI.bcast(send_buf, comm; root=0)
print("Rank $(rank) recieved this message:\n  $(recv_buf)\n")
```

+++ {"slideshow": {"slide_type": "fragment"}}

If the preceding code is saved to a file `broadcast.jl`, we can run

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: '-'
---
!~/.julia/bin/mpiexecjl --project=.. -np 2 julia broadcast.jl
```

+++ {"slideshow": {"slide_type": "fragment"}}

See also [`Bcast!`](https://juliaparallel.github.io/MPI.jl/latest/collective/#MPI.Bcast!).

+++ {"slideshow": {"slide_type": "subslide"}}

# Reduce

`Reduce` performs a parallel reduction operation.

+++ {"hide_input": false, "slideshow": {"slide_type": "-"}}

```julia
using MPI

MPI.Init()

comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

v = collect(1:size)
print("Original array on rank $(rank):\n  $(v)\n")
    

recv_buf = MPI.Reduce(v, +, comm; root=0)

if rank == 0
    print("New array on rank 0:\n  $(recv_buf)\n")
    total_sum = sum(recv_buf)
    print("Total sum:\n  $(total_sum)")
end
```

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: skip
tags: [remove_cell]
---
%%file Reduce1.jl
#!/usr/bin/env julia
using MPI

MPI.Init()

comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

v = collect(1:size)
print("Original array on rank $(rank):\n  $(v)\n")
    

recv_buf = MPI.Reduce(v, +, comm; root=0)

if rank == 0
    print("New array on rank 0:\n  $(recv_buf)\n")
    total_sum = sum(recv_buf)
    print("Total sum:\n  $(total_sum)")
end
```

+++ {"slideshow": {"slide_type": "fragment"}}

If the preceding code is saved to a file `Reduce1.jl`, we can run

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: '-'
---
!~/.julia/bin/mpiexecjl --project=.. -np 2 julia Reduce1.jl
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Custom reduction operations

+++ {"hide_input": false, "slideshow": {"slide_type": "-"}}

```julia
using MPI
MPI.Init()

import Base.+

struct Point{T}
    x::T
    y::T
end

+(A::Point{T}, B::Point{T}) where T = Point{T}(A.x + B.x, A.y + B.y)


comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)

p = Point(rank, rank) 
print("Original Point on rank $(rank):\n  $(p)\n")
    

recv_buf = MPI.Reduce(p, +, comm; root=0)

if rank == 0
    print("\nNew Point on rank 0:\n  $(recv_buf)\n")
end
```

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: skip
tags: [remove_cell]
---
%%file Reduce2.jl
#!/usr/bin/env julia
using MPI
MPI.Init()

import Base.+

struct Point{T}
    x::T
    y::T
end

+(A::Point{T}, B::Point{T}) where T = Point{T}(A.x + B.x, A.y + B.y)


comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)

p = Point(rank, rank) 
print("Original Point on rank $(rank):\n  $(p)\n")
    

recv_buf = MPI.Reduce(p, +, comm; root=0)

if rank == 0
    print("\nNew Point on rank 0:\n  $(recv_buf)\n")
end
```

+++ {"slideshow": {"slide_type": "fragment"}}

If the preceding code is saved to a file `Reduce2.jl`, we can run

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: '-'
---
!~/.julia/bin/mpiexecjl --project=.. -np 3 julia Reduce2.jl
```

+++ {"slideshow": {"slide_type": "fragment"}}

See also [`Reduce!`](https://juliaparallel.github.io/MPI.jl/latest/collective/#MPI.Reduce!) and [`Allreduce`](https://juliaparallel.github.io/MPI.jl/latest/collective/#MPI.Allreduce)/[`Allreduce!`](https://juliaparallel.github.io/MPI.jl/latest/collective/#MPI.Allreduce!).

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
