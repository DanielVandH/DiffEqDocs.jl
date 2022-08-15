# DifferentialEquations.jl: Scientific Machine Learning Enabled Simulation and Estimation

This is a suite for numerically solving differential equations written in Julia
and available for use in Julia, Python, and R. The
purpose of this package is to supply efficient Julia implementations of solvers
for various differential equations. Equations within the realm of this package
include:

- Discrete equations (function maps, discrete stochastic (Gillespie/Markov)
  simulations)
- Ordinary differential equations (ODEs)
- Split and Partitioned ODEs (Symplectic integrators, IMEX Methods)
- Stochastic ordinary differential equations (SODEs or SDEs)
- Stochastic differential-algebraic equations (SDAEs)
- Random differential equations (RODEs or RDEs)
- Differential algebraic equations (DAEs)
- Delay differential equations (DDEs)
- Neutral, retarded, and algebraic delay differential equations (NDDEs, RDDEs, and DDAEs)
- Stochastic delay differential equations (SDDEs)
- Experimental support for stochastic neutral, retarded, and algebraic delay differential equations (SNDDEs, SRDDEs, and SDDAEs)
- Mixed discrete and continuous equations (Hybrid Equations, Jump Diffusions)
- (Stochastic) partial differential equations ((S)PDEs) (with both finite
  difference and finite element methods)

The well-optimized DifferentialEquations solvers benchmark as some of the fastest
implementations, using classic algorithms and ones from recent research which
routinely outperform the "standard" C/Fortran methods, and include algorithms
optimized for high-precision and HPC applications. At the same time, it wraps
the classic C/Fortran methods, making it easy to switch over to them whenever
necessary. Solving differential equations with different methods from
different languages and packages can be done by changing one line of code,
allowing for easy benchmarking to ensure you are using the fastest method possible.

DifferentialEquations.jl integrates with the Julia package sphere with:

- GPU acceleration through CUDA.jl and DiffEqGPU.jl
- Automated sparsity detection with [SparsityDetection.jl](https://github.com/SciML/SparsityDetection.jl)
- Automatic Jacobian coloring with [SparseDiffTools.jl](https://github.com/SciML/SparseDiffTools.jl), allowing for fast solutions
  to problems with sparse or structured (Tridiagonal, Banded, BlockBanded, etc.) Jacobians
- Allowing the specification of linear solvers for maximal efficiency
- Progress meter integration with the Juno IDE for estimated time to solution
- Automatic plotting of time series and phase plots
- Built-in interpolations
- Wraps for common C/Fortran methods like Sundials and Hairer's radau
- Arbitrary precision with BigFloats and Arbfloats
- Arbitrary array types, allowing the definition of differential equations on
  matrices and distributed arrays
- Unit checked arithmetic with Unitful

Additionally, DifferentialEquations.jl comes with built-in analysis features, including:

- [Forward and Adjoint Sensitivity Analysis (Automatic Differentiation)](@ref sensitivity) for fast gradient computations
- [Parameter Estimation and Bayesian Analysis](@ref parameter_estimation)
- Neural differential equations with [DiffEqFlux.jl](https://github.com/SciML/DiffEqFlux.jl)
  for efficient scientific machine learning (scientific ML) and scientific AI.
- Automatic distributed, multithreaded, and GPU [Parallel Ensemble Simulations](@ref ensemble)
- [Global Sensitivity Analysis](@ref gsa)
- [Uncertainty Quantification](@ref uncertainty_quantification)

## Contributing
- If you're interested in contributing, please see the
  [Developer Documentation.](https://devdocs.sciml.ai/dev/)
- Please refer to the
  [SciML ColPrac: Contributor's Guide on Collaborative Practices for Community Packages](https://github.com/SciML/ColPrac/blob/master/README.md)
  for guidance on PRs, issues, and other matters relating to contributing to SciML.
- See the [SciML Style Guide](https://github.com/SciML/SciMLStyle) for common coding practices and other style decisions.
- There are a few community forums:
    - The #diffeq-bridged and #sciml-bridged channels in the
      [Julia Slack](https://julialang.org/slack/)
    - The #diffeq-bridged and #sciml-bridged channels in the
      [Julia Zulip](https://julialang.zulipchat.com/#narrow/stream/279055-sciml-bridged)
    - On the [Julia Discourse forums](https://discourse.julialang.org)
    - See also [SciML Community page](https://sciml.ai/community/)

## Supporting and Citing

The software in this ecosystem was developed as part of academic research.
If you would like to help support it, please star the repository as such
metrics may help us secure funding in the future. If you use SciML
software as part of your research, teaching, or other activities, we would
be grateful if you could cite our work.

```bibtex
@article{rackauckas2017differentialequations,
  title={Differentialequations.jl--a performant and feature-rich ecosystem for solving differential equations in julia},
  author={Rackauckas, Christopher and Nie, Qing},
  journal={Journal of Open Research Software},
  volume={5},
  number={1},
  year={2017},
  publisher={Ubiquity Press}
}
```

is necessary for any use of DifferentialEquations.jl or the packages that are
maintained as part of its suite (OrdinaryDiffEq.jl, Sundials.jl, DiffEqDevTools.jl, etc.).
Additionally, many of the solvers utilize novel algorithms, and if these algorithms 
are used we asked that you cite the methods. [Please see our citation page for guidelines](http://sciml.ai/citing.html).

## Getting Started: Installation And First Steps

### Installing from Julia

To install the package, use the following command inside the Julia REPL:
```julia
using Pkg
Pkg.add("DifferentialEquations")
```

To load the package, use the command:

```julia
using DifferentialEquations
```

This will add solvers and dependencies
for all kinds of Differential Equations (e.g. ODEs or SDEs etc., see the Supported
Equations section below). If you are interested in only one type of equation
solvers of `DifferentialEquations.jl` or simply want a more lightweight
version, see the
[Low Dependency Usage](@ref)
page.

To understand the package in more detail, check out the following tutorials in
this manual. **It is highly recommended that new users start with the
[ODE tutorial](@ref ode_example)**. Example IJulia notebooks
[can also be found in DiffEqTutorials.jl](https://github.com/SciML/DiffEqTutorials.jl).
If you find any example where there seems to be an error, please open an issue.

For the most up to date information on using the package, please join [the Gitter channel](https://gitter.im/JuliaDiffEq/Lobby).

Using the bleeding edge for the latest features and development is only recommended
for power users. Information on how to get to the bleeding edge is found in the
[developer documentation](https://juliadiffeq.github.io/DiffEqDevDocs.jl/dev/index.html#Bleeding-Edge-1).

### Installing from Python

Use of DifferentialEquations.jl from the Python programming language is available through the
[diffeqpy](https://github.com/SciML/diffeqpy) module. To install diffeqpy, use pip:

```
pip install diffeqpy
```

Using diffeqpy requires that Julia is installed and in the path, along
with DifferentialEquations.jl and PyCall.jl. To install Julia,
download a generic binary from
[the JuliaLang site](https://julialang.org/downloads/) and add it to your path.
To install Julia packages required for diffeqpy, open up Python
interpreter then run:

```pycon
>>> import diffeqpy
>>> diffeqpy.install()
```

and you're good! In addition, to improve the performance of your code it is
recommended that you use Numba to JIT compile your derivative functions. To
install Numba, use:

```
pip install numba
```

diffeqpy supports the majority of DifferentialEquations.jl with very similar
syntax, see [the diffeqpy README for more details](https://github.com/SciML/diffeqpy).
One important point to note is that Numba is generally an order of magnitude slower
than Julia in terms of  the generated differential equation solver code, and thus it is
recommended to use `julia.Main.eval` for Julia-side derivative function implementations
for maximal efficiency. See [this blog post](https://sciml.ai/news/2018/04/30/Jupyter/)
for more information.

### Installing from R

Use of DifferentialEquations.jl from the R programming language is available through the
[diffeqr](https://github.com/SciML/diffeqr) module.
[diffeqr is registered into CRAN](https://CRAN.R-project.org/package=diffeqr).
Thus to add the package, use:

```R
install.packages("diffeqr")
```

To install the master branch of the package (for developers), use:

```R
devtools::install_github('SciML/diffeqr', build_vignettes=T)
```

You will need a working installation of Julia in your path. To install Julia, download a generic binary
from [the JuliaLang site](https://julialang.org/downloads/) and add it to your path. The download and
installation of DifferentialEquations.jl will happen on the first invocation of `diffeqr::diffeq_setup()`.

Currently, use from R supported a subset of DifferentialEquations.jl which is documented
[through CRAN](https://cran.r-project.org/web/packages/diffeqr/index.html).

### IJulia Notebook Tutorials

You can access extra tutorials supplied in the
[DiffEqTutorials.jl repository](https://github.com/SciML/DiffEqTutorials.jl)
via the commands:

```julia
using Pkg
pkg"add https://github.com/SciML/SciMLTutorials.jl"
using SciMLTutorials
SciMLTutorials.open_notebooks()
```

Or you can view the webpages for the rendered tutorials at the links found in the repository.

### Video Tutorial

[![Video Tutorial](https://user-images.githubusercontent.com/1814174/36342812-bdfd0606-13b8-11e8-9eff-ff219de909e5.PNG)](https://youtu.be/KPEqYtEd-zY)

### Tutorials

The following tutorials will introduce you to the functionality of
DifferentialEquations.jl. More examples can be found by
[checking out the IJulia notebooks in the examples folder](https://github.com/SciML/DiffEqTutorials.jl).

```@contents
Pages = [
    "tutorials/ode_example.md",
    "tutorials/sde_example.md",
    "tutorials/dde_example.md",
    "tutorials/dae_example.md",
    "tutorials/discrete_stochastic_example.md",
    "tutorials/jump_diffusion.md",
    "tutorials/bvp_example.md",
    "tutorials/additional.md"
    ]
Depth = 2
```

### Removing and Reducing Compile Times

In some situations one may wish to decrease the compile time associated with DifferentialEquations.jl
usage. If that's the case, there's two strategies to employ. One strategy is to use the
[low dependency usage](https://diffeq.sciml.ai/stable/features/low_dep/). DifferentialEquations.jl
is a metapackage composed of many smaller packages, and thus one could directly use a single component,
such as `OrdinaryDiffEq.jl` for the pure Julia ODE solvers, and decrease the compile times by ignoring
the rest (note: the interface is exactly the same, except using a solver other than those in OrdinaryDiffEq.jl
will error). We recommend that downstream packages only rely on exactly the packages they need.

The other strategy is to use [PackageCompiler.jl](https://julialang.github.io/PackageCompiler.jl/dev/) to create 
a system image that precompiles the whole package. To do this, one simply does:

```julia
using PackageCompiler
PackageCompiler.create_sysimage([:DifferentialEquations,:Plots];replace_default=true)
```

Note that there are some drawbacks to adding a package in your system image, for example
the package will never update until you manually rebuild the system image again. For more
information on the consequences, 
[see this portion of the PackageCompiler manual](https://julialang.github.io/PackageCompiler.jl/dev/sysimages/#Drawbacks-to-custom-sysimages-1)

### Basics

These pages introduce you to the core of DifferentialEquations.jl and the common
interface. It explains the general workflow, options which are generally available,
and the general tools for analysis.

```@contents
Pages = [
    "basics/overview.md",
    "basics/common_solver_opts.md",
    "basics/solution.md",
    "basics/plot.md",
    "basics/integrator.md",
    "basics/problem.md",
    "basics/faq.md",
    "basics/compatibility_chart.md"
    ]
Depth = 2
```


### Problem Types

These pages describe building the problem types to define differential equations
for the solvers, and the special features of the different solution types.

```@contents
Pages = [
  "types/discrete_types.md",
  "types/ode_types.md",
  "types/dynamical_types.md",
  "types/split_ode_types.md",
  "types/steady_state_types.md",
  "types/bvp_types.md",
  "types/sde_types.md",
  "types/rode_types.md",
  "types/dde_types.md",
  "types/dae_types.md",
  "types/jump_types.md",
]
Depth = 2
```

### Solver Algorithms

These pages describe the solvers and available algorithms in detail.

```@contents
Pages = [
  "solvers/discrete_solve.md",
  "solvers/ode_solve.md",
  "solvers/dynamical_solve.md",
  "solvers/split_ode_solve.md",
  "solvers/steady_state_solve.md",
  "solvers/bvp_solve.md",
  "solvers/jump_solve.md",
  "solvers/sde_solve.md",
  "solvers/rode_solve.md",
  "solvers/dde_solve.md",
  "solvers/dae_solve.md",
  "solvers/benchmarks.md"
]
Depth = 2
```

### Additional Features

These sections discuss extra performance enhancements, event handling, and other
in-depth features.

```@contents
Pages = [
    "features/performance_overloads.md",
    "features/diffeq_arrays.md",
    "features/diffeq_operator.md",
    "features/noise_process.md",
    "features/linear_nonlinear.md",
    "features/callback_functions.md",
    "features/callback_library.md",
    "features/ensemble.md",
    "features/io.md",
    "features/low_dep.md",
    "features/progress_bar.md"
]
Depth = 2
```

### Analysis Tools

Because DifferentialEquations.jl has a common interface on the solutions, it is
easy to add functionality to the entire DiffEq ecosystem by developing it
to the solution interface. These pages describe the add-on analysis tools which
are available.

```@contents
Pages = [
    "analysis/parameterized_functions.md",
    "analysis/parameter_estimation.md",
    "analysis/bifurcation.md",
    "analysis/sensitivity.md",
    "analysis/global_sensitivity.md",
    "analysis/uncertainty_quantification.md",
    "analysis/neural_networks.md",
    "analysis/dev_and_test.md"
]
Depth = 2
```

### Modeling Tools

While DifferentialEquations.jl can be used to directly build any differential
or difference equation (/ discrete stochastic) model, in many cases it can be
helpful to have a tailored-built API for making certain types of common models
easier. This is provided by the modeling functionality.

```@contents
Pages = [
    "models/multiscale.md",
    "models/physical.md",
    "models/financial.md",
    "models/chemical_reactions.md",
    "models/external_modeling.md"
]
Depth = 2
```

### Extra Details

These are just assorted extra explanations for the curious.

```@contents
Pages = [
    "extras/timestepping.md"
    "extras/sensitivity_math.md"
]
Depth = 2
```

## Acknowledgements

#### Core Contributors

JuliaDiffEq and DifferentialEquations.jl has been a collaborative effort by many
individuals. Significant contributions have been made by the following individuals:

- Chris Rackauckas (@ChrisRackauckas) (lead developer)
- Yingbo Ma (@YingboMa)
- David Widmann (@devmotion)
- Hendrik Ranocha (@ranocha)
- Ethan Levien (@elevien)
- Tom Short (@tshort)
- @dextorious
- Samuel Isaacson (@isaacsas)

#### Google Summer of Code Alumni

- Yingbo Ma (@YingboMa)
- Shivin Srivastava (@shivin9)
- Ayush Pandey (@Ayush-iitkgp)
- Xingjian Guo (@MSeeker1340)
- Shubham Maddhashiya (@sipah00)
- Vaibhav Kumar Dixit (@Vaibhavdixit02)
