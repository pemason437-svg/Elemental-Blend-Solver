---
title: 'Elemental Blend Solver (EBS): A Browser-Based Non-Negative Least Squares Tool for Polymer Elemental Composition Matching'
tags:
  - chemometrics
  - polymer science
  - NNLS
  - elemental analysis
  - browser-based software
  - materials science
  - analytical chemistry
authors:
  - name: Paul Mason
    orcid: 0009-0002-7818-1377
    affiliation: 1
affiliations:
  - name: Independent Researcher, Cocoa, Florida, United States
    index: 1
date: March 2026
bibliography: paper.bib
repository-code: 'https://github.com/pemason437-svg/Elemental-Blend-Solver'
archive-doi: 'https://doi.org/10.5281/zenodo.19006359'
license: MIT
version: '8.0'
---

# Summary

The **Elemental Blend Solver (EBS)** is a fully self-contained, single-file HTML application that computes the optimal mass-mixing fractions of up to five source polymers whose combined elemental weight composition best approximates a chosen target molecule. Running entirely in the browser with no installation, server, or internet connection required, EBS solves a constrained Non-Negative Least Squares (NNLS) problem via the Lawson–Hanson active-set algorithm [@lawson1974]. The solver accepts any chemically defined molecule as a target and draws source materials from a curated 73-polymer database spanning 20 tracked elements (C, H, O, N, Cl, S, P, Si, F, Br, I, Na, K, Mg, Ca, Fe, Al, B, Se, Zn). Results are delivered in real time alongside six interactive graphical analysis views, eight advanced diagnostic tools, and export pathways to Word and SVG formats.

The tool was developed in the context of polymer blend characterisation and elemental composition reconstruction — tasks that arise in recycling stream analysis, process simulation, and materials research — but its formulation is fully general and applicable to any domain where a target elemental profile must be matched by a linear combination of known source compositions.

# Statement of Need

Analytical chemistry routinely produces elemental composition measurements via CHN analysis, ICP-OES, XRF, and related techniques. Reconstructing these profiles from a library of known constituents is an inverse blending problem with two hard physical constraints: all mixing fractions must be non-negative, and they must sum to unity. This constrained linear system has no general closed-form solution and requires careful numerical treatment.

Existing approaches to such problems are either embedded in expensive commercial software (e.g., proprietary formulation tools in plastics processing), implemented as standalone scripts requiring a Python or R environment [@virtanen2020], or handled by general-purpose optimisation libraries that provide no domain-specific scaffolding. There is no openly accessible, zero-install, browser-native tool that provides both the NNLS solver and the full suite of diagnostic analyses (sensitivity, substitutability, feasibility, principal components coverage) in a single distributable file.

EBS addresses this gap. Its single-file architecture means it can be shared as an email attachment or placed on any web server with no dependency management. Its 73-polymer database covers the majority of industrially relevant thermoplastics, thermosets, and engineering polymers. Its diagnostic tools — including Monte Carlo sensitivity analysis, Leave-One-Out source importance, Exhaustive Optimal Blend Search, and a PCA Database Coverage Map — transform a routine solver into a full analytical workbench.

# State of the Field

Browser-based scientific computing tools have grown considerably in capability since the widespread adoption of WebAssembly and modern JavaScript engines [@smith2017]. Projects such as Pyodide bring full Python runtimes to the browser [@pyodide2021], and tools like Observable notebooks provide interactive data analysis environments. However, these platforms require either a network connection, a runtime download, or programming literacy on the part of the end user.

Within chemometrics and polymer science specifically, the dominant software environments are MATLAB with the PLS Toolbox [@wise2006], R with packages such as `nnls` [@mullen2012], and Python's `scipy.optimize.nnls` [@virtanen2020]. Web-native implementations that integrate a solver, a curated domain database, and publication-ready export in a single redistributable file are not represented in the literature to the authors' knowledge.

EBS occupies this niche by combining zero-dependency deployment with scientific rigour: the NNLS formulation is mathematically identical to Lawson–Hanson active-set implementations in established numerical libraries, and the chi-squared fit test follows standard goodness-of-fit practice [@press2007].

# Software Design

## Mathematical Core

For a blend of $n$ source polymers with mass fractions $\mathbf{f} = (f_1, \ldots, f_n)^\top$, the achieved elemental composition is:

$$\mathbf{A}\mathbf{f} = \mathbf{b}$$

where $A_{ij}$ is the weight fraction of element $i$ in polymer $j$, and $\mathbf{b}$ is the target elemental weight-fraction vector. The mixing fractions are subject to $f_i \geq 0$ and $\sum_i f_i = 1$.

The sum-to-unity constraint is incorporated as a heavily weighted extra row appended to $\mathbf{A}$ and $\mathbf{b}$ (penalty weight $\lambda = 10$), converting the equality-constrained problem to a pure NNLS instance:

$$\tilde{\mathbf{A}} = \begin{pmatrix} \mathbf{A} \\ \lambda \mathbf{1}^\top \end{pmatrix}, \quad \tilde{\mathbf{b}} = \begin{pmatrix} \mathbf{b} \\ \lambda \end{pmatrix}$$

The augmented system is solved by the Lawson–Hanson active-set algorithm [@lawson1974], which iteratively identifies the active (non-zero) set of source polymers and solves a reduced unconstrained least-squares problem on each iteration. Version 8 applies this unified solver to all system sizes — underdetermined, square, and overdetermined — replacing the earlier multi-method approach of previous versions.

## Architecture

EBS is implemented as a single HTML file (~193 KB) containing all JavaScript logic, CSS styling, and the 73-polymer database encoded as a JSON object. No build step, package manager, or network request is required at runtime. The file is opened directly in any modern browser (Chrome, Edge, Firefox, Safari).

The application is structured around six interactive graphical views:

- **Radar Chart** — overlay of achieved versus target elemental profiles
- **Scatter Plot** — source polymer positions in two-element space
- **Build-Up Chart** — stacked visualisation of each element's contribution by source
- **Blend Error Chart** — signed per-element residuals
- **Feasibility Map** — two-dimensional slice of the convex hull in element space
- **Convergence Plot** — SSR as a function of iteration

Eight advanced diagnostic tools extend the core solver:

1. **Monte Carlo Sensitivity Analysis** — propagates uncertainty in target elemental fractions through the solver to produce confidence intervals on mixing fractions
2. **Distance-to-Hull** — quantifies how far the target lies outside the convex hull of the source set
3. **Substitution Recommender** — scans the polymer database for single-polymer replacements that reduce SSR
4. **Leave-One-Out Source Importance** — measures the SSR increase when each source is removed
5. **Global Decomposability Score** — a [0,1] scalar summarising how well the current source set can span the target
6. **PCA Database Coverage Map** — projects all 73 database polymers and the target onto a two-dimensional PCA space, revealing structural gaps in source coverage
7. **Ternary SSR Contour** (3-source mode) — visualises the error landscape over the entire 3-source simplex
8. **Exhaustive Optimal Blend Search** — enumerates all $\binom{73}{n}$ source combinations up to a user-defined $n$ and returns the globally optimal set

## Export

EBS generates a structured eight-section Word report (`.docx`) containing all solver results, numerical tables, and SVG-embedded charts, as well as standalone SVG exports of each graphical view.

# Research Impact Statement

EBS was developed to support polymer blend characterisation in the context of recycling stream reconstruction, where CHN/XRF elemental analysis of an unknown material must be matched against a library of candidate polymers. The tool is directly applicable to: (1) plastics recycling and sorting research, (2) quality control in polymer compounding, (3) educational demonstrations of constrained least-squares optimisation in analytical chemistry courses, and (4) any domain requiring elemental fingerprint matching, including geochemistry, food science, and environmental monitoring.

The 73-polymer database, covering 20 elements including heteroatoms (Si, P, Na, B, Br, I, K, Mg, Ca, Fe, Al, Se, Zn) not present in earlier versions, substantially broadens applicability beyond the C/H/O/N/Cl scope of most existing polymer analysis tools. The exhaustive blend search and substitution recommender tools support systematic database-driven discovery workflows not available in general-purpose numerical libraries.

# AI Usage Disclosure

Portions of the EBS application code and documentation were developed with AI-assisted code generation tools. All mathematical formulations, algorithmic design decisions, database curation, and validation were performed and verified by the authors.

# Acknowledgements

The author thanks the open-source scientific computing community whose foundational work on NNLS algorithms and numerical methods [@lawson1974; @press2007] underpins this software.
