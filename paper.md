---
title: 'Plyssim : a physical elastic surface simulator'
tags:
  - C++
  - Python
  - Mesh
  - Elastic
  - Simulation
authors:
  - name: Serge Dmitrieff^[*]
    orcid: 0000-0002-1362-5670
    affiliation: "1"
affiliations:
 - name: Institut Jacques Monod CNRS UMR7592 and Universite Paris Diderot, 75205 Paris Cedex 13 France
   index: 1
date: 30 January 2020
bibliography: paper.bib
---

# Summary

Motivated by the study of cell wall mechanics, we developed a simple particle-based simulator of discretized elastic surfaces. The surfaces can be either thin (triangulated surface) or thick (a tetrahedral shell). Thin shells can also have a custom flexural rigidity to mimick non-isotropic material.  Additionally, physical constraints such as confinement and pressure can be applied to the surface.  Configuration files have the yaml file format.  Input and output files are based on the simple and widely used ply file format. A command-line tool for rapid ply file manipulation is also provided.

# Statement of need


A surface is a 2D manifold embedded in a 3D space. Because of this dimensionality discrepancy,  the dynamical simulation of surface mechanics can be challenging, whether elastic surfaces (paper, cloth, cell walls), or fluid surfaces such as membranes. In the latter case, the equilibrium state of axisymmetric shapes can be found numerically as a 1D, third order partial differential problem ; however, this simple method is not applicable in the absence of symmetry. A popular approach to model elastic surfaces is to discretize them (for instance as triangulated surfaces), and solve the dynamics of their vertices. This has been performed successfully for lipid membranes, and yeast cell walls. Surprisingly,  while a plethora of tools exist to create, visualize, and manipulate mesh files, straightfoward and easy to use implementations of elastic triangulated surfaces for a broad audience have been lacking.

In this article, we describe Plyssim, a such elastic mesh mechanics simulator, designed for non-technical users. We also provide Plyconvert, a command line (and python) tool to perform simple pre-operations on ply meshes, such as scaling, centering, aligning.

# Implementation

Plyssim is implemented in C++14. It relies on Yaml-cpp to read the configuration files, on tiny-ply to import and export meshes from ply files, and on Aboria as a back-end for particle description and linear algebra.

The logical implementation is as follows : Plyssim  iterates time-dependent simulations (called **runs** on **meshes**, instances of class *Mesh*. A mesh contains pointers towards a set of particles (mesh.mesh\_parts) and properties (mesh.mesh\_props). By default mesh.mesh\_parts is an instance of class *Part\_set*, but according to the physics to be simulated, mesh.mesh\_parts can be an instance of any specialization of that class. Three specializations are currently available : thin elastic triangular mesh (**type 2** : class *Elastic\_spin\_parts*), thin elastic triangular mesh with bending rigidity (**type 3** : class *Elastic\_triangle\_part_\set*) and thick elastic tetrahedral mesh (**type 4** : class *Tetr\_elastic\_part\_set*).   A particle set class such as *Part\_set* is a container for an Aboria particle set and additional variable and methods to compute forces and dynamics.

A mesh property is an instance of a specialization of class *Part\_set\_props*, and contains information such as the physical properties of the mesh to be simulated, such as Young Modulus, viscosity, etc.

Each run has properties are instances of the class *Simul\_props* (or its specialization), containing information such as timestep, pressure, confinement, duration, etc. For all runs, the simulation iterates on all meshes.

The physics of the various kinds of meshes is described in more details in a research article. In brief, we compute the forces from the stretching of the edges of the mesh, that are implemented as non-linear springs. Bending is implemented between mesh faces for type 3. Pressure forces are computed on each vertex (type 2,3), and are proportional to the surface area around the vertex. For thick meshes (type 4), pressure forces are computed only triangular faces, and not on tetrahedral volume elements.  Confinement forces are applied on all vertices outside the confinement volume and are proportional to the surface area around the vertex. The displacement of a vertex is computed for each timestep according to a first order Euler algorithm.

## Availability

Plyssim is available on [github](https://github.com/SergeDmi/Plyssim), and compiles on Mac and Linux, with Clang, GCC, and ICC (albeit slower with ICC) provided required libraries are installed. A docker image is provided on [dockerhub](https://hub.docker.com/r/sergedmi/plyssim).

# Plyconvert
PlyConvert is a simple command-line utility tool to perform simple manipulations on ply files. It is written in Python 3 and uses the plydata structure from the package plyfile as a backend. Typically it is used to re-scale, align, rename or convert ply files. This can be performed for a single file, or in batch, searching recursively in folders.

Single dollars ($) are required for inline mathematics e.g. $f(x) = e^{\pi/x}$

Double dollars make self-standing equations:

$$\Theta(x) = \left\{\begin{array}{l}
0\textrm{ if } x < 0\cr
1\textrm{ else}
\end{array}\right.$$

You can also use plain \LaTeX for equations
\begin{equation}\label{eq:fourier}
\hat f(\omega) = \int_{-\infty}^{\infty} f(x) e^{i\omega x} dx
\end{equation}
and refer to \autoref{eq:fourier} from text.

# Citations

Citations to entries in paper.bib should be in
[rMarkdown](http://rmarkdown.rstudio.com/authoring_bibliographies_and_citations.html)
format.

If you want to cite a software repository URL (e.g. something on GitHub without a preferred
citation) then you can do it with the example BibTeX entry below for @fidgit.

For a quick reference, the following citation commands can be used:
- `@author:2001`  ->  "Author et al. (2001)"
- `[@author:2001]` -> "(Author et al., 2001)"
- `[@author1:2001; @author2:2001]` -> "(Author1 et al., 2001; Author2 et al., 2002)"

# Figures

Figures can be included like this:
![Caption for example figure.\label{fig:example}](figure.png)
and referenced from text using \autoref{fig:example}.

Figure sizes can be customized by adding an optional second parameter:
![Caption for example figure.](figure.png){ width=20% }

# Acknowledgements

S.D. aknowledges Nicolas Minc for getting him interested in cell wall mechanics, the entire Minc lab past and present for interactions, and CNRS-Momentum for the funding.

# References
