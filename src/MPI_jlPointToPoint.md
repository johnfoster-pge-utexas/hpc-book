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

# Point-to-Point Communication

Sending data from one point (process/task) to another point (process/task)

+++ {"slideshow": {"slide_type": "subslide"}}

## send/recv

+++ {"hide_input": false, "slideshow": {"slide_type": "-"}}

```julia
using MPI
MPI.Init()

comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

struct Point
  x::Float64
  y::Float64
end

p = Point(rank, rank)

if rank == 0
    MPI.send(p, comm; dest=1)
elseif rank > 0
    data = MPI.recv(comm; source=(rank - 1))
    MPI.send(p, comm; dest=(rank + 1) % size)
end

if rank == 0
    data = MPI.recv(comm; source=(size - 1))
end

print("My rank is $(rank)\n I received this: $(data)\n")
MPI.Barrier(comm)
```

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: skip
tags: [remove_cell]
---
%%file send_recv.jl
#!/usr/bin/env julia
using MPI
MPI.Init()

comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)

struct Point
  x::Float64
  y::Float64
end

p = Point(rank, rank)

if rank == 0
    MPI.send(p, comm; dest=1)
elseif rank > 0
    data = MPI.recv(comm; source=(rank - 1))
    MPI.send(p, comm; dest=(rank + 1) % size)
end

if rank == 0
    data = MPI.recv(comm; source=(size - 1))
end

print("My rank is $(rank)\n I received this: $(data)\n")
MPI.Barrier(comm)
```

+++ {"slideshow": {"slide_type": "fragment"}}

If the preceding code is saved as a file [send_recv.jl](send_recv.jl), we can run

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: '-'
---
!~/.julia/bin/mpiexecjl --project=.. -np 3 julia send_recv.jl
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Send/Recv

+++ {"hide_input": false, "slideshow": {"slide_type": "-"}}

```julia
using MPI
MPI.Init()

comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)


send_buf = Array{Float64}(undef, 10)
recv_buf = Array{Float64}(undef, 10)

fill!(send_buf, Float64(rank))

if rank == 0
    MPI.Send(send_buf, comm; dest=1)
elseif rank > 0
    MPI.Recv!(recv_buf, comm; source=(rank - 1))
    MPI.Send(send_buf, comm; dest=(rank + 1) % size)
end

if rank == 0
    MPI.Recv!(recv_buf, comm; source=(size - 1))
end

print("My rank is $(rank)\n I received this: $(recv_buf)\n")
MPI.Barrier(comm)
```

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: skip
tags: [remove_cell]
---
%%file Send_Recv.jl
#!/usr/bin/env julia
using MPI
MPI.Init()

comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)


send_buf = Array{Float64}(undef, 10)
recv_buf = Array{Float64}(undef, 10)

fill!(send_buf, Float64(rank))

if rank == 0
    MPI.Send(send_buf, comm; dest=1)
elseif rank > 0
    MPI.Recv!(recv_buf, comm; source=(rank - 1))
    MPI.Send(send_buf, comm; dest=(rank + 1) % size)
end

if rank == 0
    MPI.Recv!(recv_buf, comm; source=(size - 1))
end

print("My rank is $(rank)\n I received this: $(recv_buf)\n")
MPI.Barrier(comm)
```

+++ {"slideshow": {"slide_type": "fragment"}}

If the preceding code is saved as a file [Send_Recv.jl](Send_Recv.jl), we can run

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: '-'
---
!~/.julia/bin/mpiexecjl -np 3 julia Send_Recv.jl
```

+++ {"slideshow": {"slide_type": "subslide"}}

## Isend/Irecv

+++ {"hide_input": false, "slideshow": {"slide_type": "-"}}

```julia
using MPI
MPI.Init()

comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)


send_buf = Array{Float64}(undef, 10)
recv_buf = Array{Float64}(undef, 10)

fill!(send_buf, Float64(rank))

if rank == 0
    send_status = MPI.Isend(send_buf, comm; dest=1)
elseif rank > 0
    recv_status = MPI.Irecv!(recv_buf, comm; source=(rank - 1))
    send_status = MPI.Isend(send_buf, comm; dest=(rank + 1) % size)
end

if rank == 0
    recv_status = MPI.Irecv!(recv_buf, comm; source=(size - 1))
end

stats = MPI.Waitall!([send_status, recv_status])

print("My rank is $(rank)\n I received this: $(recv_buf)\n")
```

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: skip
tags: [remove_cell]
---
%%file Isend_Irecv.jl
#!/usr/bin/env julia
using MPI
MPI.Init()

comm = MPI.COMM_WORLD
rank = MPI.Comm_rank(comm)
size = MPI.Comm_size(comm)


send_buf = Array{Float64}(undef, 10)
recv_buf = Array{Float64}(undef, 10)

fill!(send_buf, Float64(rank))

if rank == 0
    send_status = MPI.Isend(send_buf, comm; dest=1)
elseif rank > 0
    recv_status = MPI.Irecv!(recv_buf, comm; source=(rank - 1))
    send_status = MPI.Isend(send_buf, comm; dest=(rank + 1) % size)
end

if rank == 0
    recv_status = MPI.Irecv!(recv_buf, comm; source=(size - 1))
end

stats = MPI.Waitall!([send_status, recv_status])

print("My rank is $(rank)\n I received this: $(recv_buf)\n")
```

+++ {"slideshow": {"slide_type": "fragment"}}

If the preceding code is saved as a file `Isend_Irecv.jl`, we can run

```{code-cell} ipython3
---
hide_input: false
slideshow:
  slide_type: '-'
---
!~/.julia/bin/mpiexecjl -np 3 julia ISend_IRecv.jl
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
