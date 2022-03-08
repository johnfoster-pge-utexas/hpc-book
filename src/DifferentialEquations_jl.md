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

# Differential Equations.jl

[DifferentialEquations.jl](https://diffeq.sciml.ai/stable/) is likely the most sophisticated differential equation solver suite available in any language.  There are more than 200 methods implemented that are capable of solving all types of differential equations including

 * Discrete equations (function maps, discrete stochastic (Gillespie/Markov) simulations)
 * Ordinary differential equations (ODEs)
 * Split and Partitioned ODEs (Symplectic integrators, IMEX Methods)
 * Stochastic ordinary differential equations (SODEs or SDEs)
 * Stochastic differential-algebraic equations (SDAEs)
 * Random differential equations (RODEs or RDEs)
 * Differential algebraic equations (DAEs)
 * Delay differential equations (DDEs)
 * Neutral, retarded, and algebraic delay differential equations (NDDEs, RDDEs, and DDAEs)
 * Stochastic delay differential equations (SDDEs)
 * Experimental support for stochastic neutral, retarded, and algebraic delay differential equations (SNDDEs, SRDDEs, and SDDAEs)
 * Mixed discrete and continuous equations (Hybrid Equations, Jump Diffusions)
 * (Stochastic) partial differential equations ((S)PDEs) (with both finite difference and finite element methods)
 
See [documentation](https://diffeq.sciml.ai/stable/) for more.

+++ {"slideshow": {"slide_type": "slide"}}

## Install

[DifferentialEquations.jl](https://diffeq.sciml.ai/stable/) is not installed by default when Julia is installed.  To ensure it's installed, either run

```julia
using Pkg; Pkg.add("DifferentialEquations")
```

from the Julia REPL or enter the package manager with `]` and then run

```julia
add DifferentialEquations
```

+++ {"slideshow": {"slide_type": "slide"}}

## Example - Robertson Equations

\begin{align}
\frac{dy_1}{dt} &= -0.04y_1 + 10^4 y_2 y_3 \\
\frac{dy_2}{dt} &=  0.04y_1 - 10^4 y_2 y_3 - 3 * 10^7 y_2^2 \\
\frac{dy_3}{dt} &=  3 * 10^7 y_2^2
\end{align}

+++ {"slideshow": {"slide_type": "fragment"}}

Define the system of equations.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function rober!(du,u,p,t)
  y₁,y₂,y₃ = u
  k₁,k₂,k₃ = p
  du[1] = -k₁*y₁+k₃*y₂*y₃
  du[2] =  k₁*y₁-k₂*y₂^2-k₃*y₂*y₃
  du[3] =  k₂*y₂^2
  nothing
end
```

+++ {"slideshow": {"slide_type": "fragment"}}

Define the ODE problem.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
using DifferentialEquations

u₀ = [1.0,0.0,0.0]
tspan = (0.0,1e5)
params = [0.04,3e7,1e4]

ode_prob = ODEProblem(rober!,u₀,tspan,params);
```

+++ {"slideshow": {"slide_type": "fragment"}}

Solve and plot

```{code-cell}
---
slideshow:
  slide_type: '-'
---
using Plots

ode_sol = solve(ode_prob)
plot(ode_sol, tspan=(1e-6,1e5), xscale=:log10, layout=(3,1))
```

+++ {"slideshow": {"slide_type": "subslide"}}

### User defined Jacobian

The Jacobian $J$ of a function $f(\mathbf{x})$ is

$$
J_{ij} = \frac{\partial f(\mathbf{x})_i}{\partial x_j}
$$

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function rober_jac!(J,u,p,t)
  y₁,y₂,y₃ = u
  k₁,k₂,k₃ = p
  J[1,1] = k₁ * -1
  J[2,1] = k₁
  J[3,1] = 0
  J[1,2] = y₃ * k₃
  J[2,2] = y₂ * k₂ * -2 + y₃ * k₃ * -1
  J[3,2] = y₂ * 2 * k₂
  J[1,3] = k₃ * y₂
  J[2,3] = k₃ * y₂ * -1
  J[3,3] = 0
  nothing
end
```

+++ {"slideshow": {"slide_type": "fragment"}}

Define the function and problem

```{code-cell}
---
slideshow:
  slide_type: '-'
---
ode_fun_jac = ODEFunction(rober!, jac=rober_jac!)
ode_prob_jac = ODEProblem(ode_fun_jac, u₀, tspan, params);
```

+++ {"slideshow": {"slide_type": "fragment"}}

Solve and plot, this time specifying the solver.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
ode_sol_jac = solve(ode_prob_jac, Rosenbrock23())
plot(ode_sol_jac, tspan=(1e-6,1e5), xscale=:log10, layout=(3,1))
```

+++ {"slideshow": {"slide_type": "fragment"}}

Benchmark the two solves.

```{code-cell}
using BenchmarkTools;

@btime solve(ode_prob, Rosenbrock23());
@btime solve(ode_prob_jac, Rosenbrock23());
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Automatic differentiation Jacobian

```{code-cell}
---
slideshow:
  slide_type: '-'
---
using ForwardDiff

function rober_jac_auto!(J,u,p,t)
  out = zero(u)
  ForwardDiff.jacobian!(J, (y, x) -> rober!(y, x, p, t), out, u) 
  nothing
end
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
ode_fun_ad_jac = ODEFunction(rober!, jac=rober_jac_auto!)
ode_prob_ad_jac = ODEProblem(ode_fun_ad_jac, u₀, tspan, params);
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
ode_sol_ad_jac = solve(ode_prob_ad_jac, Rosenbrock23())
plot(ode_sol_ad_jac, tspan=(1e-6,1e5), xscale=:log10, layout=(3,1))
```

+++ {"slideshow": {"slide_type": "fragment"}}

Benchmark all three solves

```{code-cell}
---
slideshow:
  slide_type: '-'
---
@btime solve(ode_prob, Rosenbrock23());
@btime solve(ode_prob_jac, Rosenbrock23());
@btime solve(ode_prob_ad_jac, Rosenbrock23());
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Solve with StaticArrays

This approach can work well for small systems (< 20 ODEs)

```{code-cell}
---
slideshow:
  slide_type: '-'
---
using StaticArrays

function rober_sa(u, p, t)
  y₁,y₂,y₃ = u
  k₁,k₂,k₃ = p
  dy₁ = -k₁*y₁+k₃*y₂*y₃
  dy₂ =  k₁*y₁-k₂*y₂^2-k₃*y₂*y₃
  dy₃ =  k₂*y₂^2
    
  SA[dy₁, dy₂, dy₃]
end

function rober_jac_auto_sa(u,p,t)
  ForwardDiff.jacobian(x -> rober_sa(x, p, t), u)
end
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
ode_fun_sa = ODEFunction(rober_sa, jac=rober_jac_auto_sa, jac_prototype=StaticArray)

u₀ = SA[1.0,0.0,0.0]
ode_prob_sa = ODEProblem(ode_fun_sa, u₀,tspan,params);
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
@btime solve(ode_prob, Rosenbrock23());
@btime solve(ode_prob_jac, Rosenbrock23());
@btime solve(ode_prob_ad_jac, Rosenbrock23());
@btime solve(ode_prob_sa, Rosenbrock23());
```

+++ {"slideshow": {"slide_type": "slide"}}

## Mass matrix ODEs 

+++ {"slideshow": {"slide_type": "-"}}

Normally we seek to write our ODEs in the form

$$
u' = f(u, p, t)
$$

However, sometimes it's common to have an equation in the form

$$
M u' = f(u, p, t)
$$

where $M$ is known as the *mass matrix*.  We can rewrite the Robertson equations as


\begin{align}
\frac{dy_1}{dt} &= -0.04y_1 + 10^4 y_2 y_3 \\
\frac{dy_2}{dt} &=  0.04y_1 - 10^4 y_2 y_3 - 3 * 10^7 y_2^2 \\
1 &= y_1 + y_2 + y_3
\end{align}

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function rober_dae!(du,u,p,t)
  y₁,y₂,y₃ = u
  k₁,k₂,k₃ = p
  du[1] = -k₁*y₁ + k₃*y₂*y₃
  du[2] =  k₁*y₁ - k₃*y₂*y₃ - k₂*y₂^2
  du[3] =  y₁ + y₂ + y₃ - 1
  nothing
end
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
M = [1. 0  0
     0  1. 0
     0  0  0]

u₀ = [1.0,0.0,0.0]

ode_fun_mm = ODEFunction(rober_dae!, mass_matrix=M)
ode_prob_mm = ODEProblem(ode_fun_mm, u₀, tspan, params)

ode_sol_mm = solve(ode_prob_mm,Rodas5(),reltol=1e-8,abstol=1e-8)
plot(ode_sol_mm, xscale=:log10, tspan=(1e-6, 1e5), layout=(3,1))
```

+++ {"slideshow": {"slide_type": "slide"}}

## Implicitly defined ODEs/DAEs

In this example we'll write the Robertson equation in an implicit (also known as residual) form

$$
f(du, u, p, t) = 0
$$

Rewriting the Robertson equations in this form we have

\begin{align}
0  &= -0.04y_1 + 10^4 y_2 y_3 - \frac{dy_1}{dt} \\
0  &=  0.04y_1 - 10^4 y_2 y_3 - 3 * 10^7 y_2^2 - \frac{dy_2}{dt} \\
0 &= y_1 + y_2 + y_3 - 1
\end{align}

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function rober_implicit_dae!(out, du, u, p, t)
  dy₁, dy₂, dy₃ = du
  y₁, y₂, y₃ = u
  k₁, k₂, k₃ = p
    
  out[1] = -k₁*y₁ + k₃*y₂*y₃ - dy₁
  out[2] =  k₁*y₁ - k₃*y₂*y₃ - k₂*y₂^2 - dy₂
  out[3] =  y₁ + y₂ + y₃ - 1
  nothing  
end
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
du₀ = [-0.04, 0.04, 0.0]

implicit_dae_prob = DAEProblem(rober_implicit_dae!, du₀, u₀,tspan, params, 
                               differential_vars=[true,true,false]);

implicit_dae_sol = solve(implicit_dae_prob)
plot(implicit_dae_sol, xscale=:log10, tspan=(1e-6, 1e5), layout=(3,1))
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Implicit ODE/DAE with Jacobian

The Jacobian of an implicit ODE/DAE written in the form

$$
f(d\mathbf{u}, d\mathbf{u}, p, t) = 0
$$

is

$$
J_{ij} = \gamma \frac{\partial f_i}{\partial(du)_j} + \frac{\partial f_i}{\partial u_j}
$$

where $\gamma$ is supplied by the solver.

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function rober_implicit_dae_jacobian!(J,du,u,p,gamma,t)
  #J = gamma*df/d(du) + df/du
  dy₁, dy₂, dy₃ = du
  y₁, y₂, y₃ = u
  k₁, k₂, k₃ = p  
    
  J[1,1] = -gamma - k₁
  J[1,2] = k₃*y₃
  J[1,3] = k₃*y₂
  J[2,1] = k₁
  J[2,2] = -gamma - k₃*y₃ - 2*k₂*y₂
  J[2,3] = -k₃*y₂
  J[3,1] = 1
  J[3,2] = 1
  J[3,3] = 1
end
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
dae_implicit_fun_jac = DAEFunction(rober_implicit_dae!, jac=rober_implicit_dae_jacobian!)
implicit_dae_prob_jac = DAEProblem(dae_implicit_fun_jac, du₀, u₀, tspan, params, 
                                   differential_vars=[true,true,false]);
implicit_dae_sol_jac = solve(implicit_dae_prob_jac)
plot(implicit_dae_sol_jac, xscale=:log10, tspan=(1e-6, 1e5), layout=(3,1))
```

+++ {"slideshow": {"slide_type": "subslide"}}

### Automatic differentiation Jacobian (StaticArrays)

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function rober_implicit_dae(du, u, p, t)
  dy₁, dy₂, dy₃ = du
  y₁, y₂, y₃ = u
  k₁, k₂, k₃ = p
    
  SA[-k₁*y₁ + k₃*y₂*y₃ - dy₁,
     k₁*y₁ - k₃*y₂*y₃ - k₂*y₂^2 - dy₂,
     y₁ + y₂ + y₃ - 1]
end
```

```{code-cell}
---
slideshow:
  slide_type: '-'
---
function rober_implicit_dae_ad_jacobian!(J,du,u,p,gamma,t)
  #J = gamma*df/d(du) + df/du
  ForwardDiff.jacobian!(J, x -> rober_implicit_dae(x, u, p, t), du) 
  J .*= gamma
    
  J .+= ForwardDiff.jacobian(x -> rober_implicit_dae(du, x, p, t), u) 
  nothing
end
```

```{code-cell}
---
slideshow:
  slide_type: fragment
---
u₀ = SA[1.0, 0.0, 0.0]
du₀ = SA[-0.04, 0.04, 0.0]

dae_implicit_fun_ad_jac_sa = DAEFunction(rober_implicit_dae, 
                                         jac=rober_implicit_dae_ad_jacobian!,
                                         jac_prototype=StaticArray)
implicit_dae_prob_ad_jac_sa = DAEProblem(dae_implicit_fun_ad_jac_sa, du₀, u₀, tspan, params, 
                                      differential_vars=[true,true,false]);

implicit_dae_sol_ad_jac_sa = solve(implicit_dae_prob_ad_jac_sa)
plot(implicit_dae_sol_ad_jac_sa, xscale=:log10, tspan=(1e-6, 1e5), layout=(3,1))
```

+++ {"slideshow": {"slide_type": "fragment"}}

Benchmarks

```{code-cell}
---
slideshow:
  slide_type: '-'
---
@btime solve(implicit_dae_prob)
@btime solve(implicit_dae_prob_jac);
@btime solve(implicit_dae_prob_ad_jac_sa);
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
