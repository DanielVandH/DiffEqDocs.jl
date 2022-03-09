# [I/O: Saving and Loading Solution Data](@id io)

The ability to save and load solutions is important for handling large datasets
and analyzing the results over multiple Julia sessions. This page explains the
existing functionality for doing so.

## Tabular Data: IterableTables

An interface to [IterableTables.jl](https://github.com/davidanthoff/IterableTables.jl)
is provided. This IterableTables link allows you to use a solution
type as the data source to convert to other tabular data formats. For example,
let's solve a 4x2 system of ODEs and get the DataFrame:

```julia-repl
using OrdinaryDiffEq, DataFrames
f_2dlinear = (du,u,p,t) -> du.=1.01u;
prob = ODEProblem(f_2dlinear,rand(2,2),(0.0,1.0));
sol1 =solve(prob,Euler();dt=1//2^(4));
df = DataFrame(sol1)
```

```
│ Row │ timestamp │ value 1  │ value 2  │ value 3  │ value 4  │
├─────┼───────────┼──────────┼──────────┼──────────┼──────────┤
│ 1   │ 0.0       │ 0.110435 │ 0.569561 │ 0.918336 │ 0.508044 │
│ 2   │ 0.0625    │ 0.117406 │ 0.605515 │ 0.976306 │ 0.540114 │
│ 3   │ 0.125     │ 0.124817 │ 0.643738 │ 1.03794  │ 0.574208 │
│ 4   │ 0.1875    │ 0.132696 │ 0.684374 │ 1.10345  │ 0.610455 │
│ 5   │ 0.25      │ 0.141073 │ 0.727575 │ 1.17311  │ 0.64899  │
│ 6   │ 0.3125    │ 0.149978 │ 0.773503 │ 1.24716  │ 0.689958 │
│ 7   │ 0.375     │ 0.159445 │ 0.822331 │ 1.32589  │ 0.733511 │
│ 8   │ 0.4375    │ 0.16951  │ 0.87424  │ 1.40959  │ 0.779814 │
│ 9   │ 0.5       │ 0.18021  │ 0.929427 │ 1.49857  │ 0.82904  │
│ 10  │ 0.5625    │ 0.191586 │ 0.988097 │ 1.59316  │ 0.881373 │
│ 11  │ 0.625     │ 0.20368  │ 1.05047  │ 1.69373  │ 0.93701  │
│ 12  │ 0.6875    │ 0.216537 │ 1.11678  │ 1.80065  │ 0.996159 │
│ 13  │ 0.75      │ 0.230206 │ 1.18728  │ 1.91432  │ 1.05904  │
│ 14  │ 0.8125    │ 0.244738 │ 1.26222  │ 2.03516  │ 1.12589  │
│ 15  │ 0.875     │ 0.260187 │ 1.3419   │ 2.16363  │ 1.19697  │
│ 16  │ 0.9375    │ 0.276611 │ 1.42661  │ 2.30021  │ 1.27252  │
│ 17  │ 1.0       │ 0.294072 │ 1.51667  │ 2.44541  │ 1.35285  │
```

If we set `syms` in the DiffEqFunction, then those names will be used:

```julia-repl
f = ODEFunction(f_2dlinear,syms=[:a,:b,:c,:d])
prob = ODEProblem(f,rand(2,2),(0.0,1.0));
sol1 =solve(prob,Euler();dt=1//2^(4));
df = DataFrame(sol1)
```

```
17×5 DataFrame
│ Row │ timestamp │ a        │ b        │ c       │ d          │
│     │ Float64   │ Float64  │ Float64  │ Float64 │ Float64    │
├─────┼───────────┼──────────┼──────────┼─────────┼────────────┤
│ 1   │ 0.0       │ 0.203202 │ 0.348326 │ 0.58971 │ 0.00606127 │
⋮
│ 16  │ 0.9375    │ 0.508972 │ 0.87247  │ 1.47708 │ 0.015182   │
│ 17  │ 1.0       │ 0.541101 │ 0.927544 │ 1.57032 │ 0.0161403  │
```

Many modeling frameworks will automatically set `syms` for this feature.
Additionally, this data can be saved to a CSV:

```julia
using CSV
CSV.write("out.csv",df)
```

## JLD2 and BSON.jl

JLD2.jl and BSON.jl will work with the full solution type if you bring the required functions
back into scope before loading. For example, if we save the solution:

```julia
using OrdinaryDiffEq, JLD2
f(u,p,t) = 1.01*u
u0=1/2
tspan = (0.0,1.0)
prob = ODEProblem(f,u0,tspan)
sol = solve(prob,Tsit5(),reltol=1e-8,abstol=1e-8)
@save "out.jld2" sol
```

then we can get the full solution type back, interpolations and all,
if we load the dependent functions first:

```julia
using JLD2
using OrdinaryDiffEq
f(u,p,t) = 1.01*u
JLD2.@load "out.jld2" sol
```

The example with BSON.jl is:

```julia
using OrdinaryDiffEq
f_2dlinear = (du,u,p,t) -> du.=1.01u
prob = ODEProblem(f_2dlinear,rand(2,2),(0.0,1.0))
sol1 =solve(prob,Euler();dt=1//2^(4))

using BSON
bson("test.bson",Dict(:sol1=>sol1))

# New session
using OrdinaryDiffEq, LinearAlgebra
using BSON
BSON.load("test.bson")
```

If you load it without the DE function then for some algorithms the
interpolation may not work, and for all algorithms you'll need
at least a solver package or SciMLBase.jl in scope in order for
the solution interface (plot recipes, array indexing, etc.) to
work. If none of these are put into scope, the solution type
will still load and hold all of the values (so `sol.u` and `sol.t`
will work), but none of the interface will be available.

## JLD

Don't use JLD. It's dead. Julia types can be saved via JLD.jl.
However, they cannot save types which have functions, which means that
the solution type is currently not compatible with JLD.

```julia
using JLD
JLD.save("out.jld","sol",sol)
```
