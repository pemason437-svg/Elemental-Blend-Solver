# Elemental Blend Solver (EBS) v8.0.3

**A browser-native, zero-install NNLS solver for polymer elemental composition matching.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://github.com/pemason437-svg/Elemental-Blend-Solver/blob/main/LICENSE.txt)
[![Version](https://img.shields.io/badge/version-8.0.3-blue.svg)](https://github.com/pemason437-svg/Elemental-Blend-Solver)
[![No Dependencies](https://img.shields.io/badge/dependencies-none-brightgreen.svg)](https://github.com/pemason437-svg/Elemental-Blend-Solver)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19075263.svg)](https://doi.org/10.5281/zenodo.19075263)

---

## What It Does

The Elemental Blend Solver computes the optimal mass-mixing fractions of up to **five source polymers** whose combined elemental weight composition best matches a chosen **target molecule**. It solves a constrained Non-Negative Least Squares (NNLS) optimisation problem using the Lawson–Hanson active-set algorithm, guaranteeing non-negative fractions that sum to exactly 1.0.

Typical use cases include:

* **Plastics recycling** — matching an unknown recyclate's elemental fingerprint (from CHN/XRF analysis) against a polymer library
* **Process simulation** — constructing a polymer blend recipe that reproduces a target feedstock composition
* **Analytical chemistry education** — visualising constrained optimisation and convex-hull feasibility

---

## How to Run

1. Download **`molecular_solver_v8_0_3.html`** to any folder on your computer
2. Double-click it to open in your browser

That's it. No installation, no npm, no Python, no internet connection required. The entire application — solver, database, charts, and export — is contained in the single HTML file.

**Tested browsers:** Chrome ≥ 80, Edge ≥ 80, Firefox ≥ 78, Safari ≥ 14

---

## Features

### Solver

* Non-Negative Least Squares via Lawson–Hanson active-set algorithm
* Up to 5 source polymers simultaneously
* **20 tracked elements:** C, H, O, N, Cl, S, P, Si, F, Br, I, Na, K, Mg, Ca, Fe, Al, B, Se, Zn
* **73-polymer database** covering commodity thermoplastics, engineering polymers, thermosets, and specialty materials
* 8 target presets + custom molecular formula entry
* Real-time results — updates instantly as you change any input

### Graphical Analysis (6 views)

| View | Description |
| --- | --- |
| Radar Chart | Achieved vs. target elemental profile overlay |
| Scatter Plot | Source polymer positions in two-element composition space |
| Build-Up Chart | Per-source contribution to each element |
| Blend Error Chart | Signed per-element residuals |
| Feasibility Map | Two-dimensional convex-hull cross-section |
| Convergence Plot | SSR vs. Lawson–Hanson iteration count |

### Advanced Diagnostic Tools (8 tools)

| Tool | Description |
| --- | --- |
| Monte Carlo Sensitivity | Propagates source-composition uncertainty to mixing-fraction confidence intervals (2,000 replicates, σ = 2% multiplicative noise) |
| Distance-to-Hull | L2 distance from target to nearest point on source convex hull |
| Substitution Recommender | Scans all 73 database polymers for single-polymer replacements that reduce SSR |
| Leave-One-Out Importance | SSR increase when each active source is removed |
| Global Decomposability Score | Dimensionless [0, 1] accept/reject metric: GDS = max(0, 1 − RMSE/‖**b**‖₂). GDS ≥ 0.97 excellent; 0.92–0.97 acceptable; 0.80–0.92 marginal; < 0.80 poor |
| PCA Database Coverage Map | All 73 polymers + target projected onto 2D PCA space with convex hull of active sources |
| Ternary SSR Contour | Heat-map of the error surface over the full 3-source simplex (3-source mode) |
| Exhaustive Optimal Blend Search | Enumerates all C(55, n) eligible combinations to find the globally optimal source set (18 polymers excluded for foreign elements) |

### Export

* **Word Report (.docx)** — 8-section formal report with all charts embedded as SVG
* **SVG Charts** — all 6 graphical views as standalone SVG files
* Entirely client-side — no server required

---

## Mathematical Background

For a blend of *n* source polymers with mass fractions **f** = (f₁, ..., fₙ)ᵀ, the achieved elemental composition is **Af = b**, where **A** is the elemental weight-fraction matrix and **b** is the target vector. The problem is:

> minimise ‖**Af** − **b**‖² subject to fᵢ ≥ 0 ∀i, Σfᵢ = 1

The sum constraint is incorporated as a weighted penalty row (λ = 10), and the resulting pure NNLS system is solved by the Lawson–Hanson algorithm.

Full mathematical derivation, annotated source code listings, and a complete user guide are in the documentation below.

---

## Documentation

| Document | Description |
| --- | --- |
| **[EBS\_User\_Guide\_v8\_0\_3.docx](docs/EBS_User_Guide_v8_0_3.docx)** | Complete technical reference and user guide. Covers quick-start, all UI features, full mathematical framework with annotated JavaScript code listings, advanced tool documentation (including corrected χ² df = m − n_active and GDS formula), troubleshooting, glossary, and software verification report. |
| **[Math\_used\_in\_the\_molecular\_solver\_code\_v8\_0\_3.docx](docs/Math_used_in_the_molecular_solver_code_v8_0_3.docx)** | Detailed mathematical derivations underpinning the solver code. Covers the NNLS formulation, Lawson–Hanson active-set algorithm, sum constraint penalty, corrected chi-squared degrees of freedom (df = m − n_active), Global Decomposability Score (GDS = max(0, 1 − RMSE/‖b‖₂)), and all supporting calculations used in the implementation. |

---

## Repository Structure

```
Elemental-Blend-Solver/
├── molecular_solver_v8_0_3.html    ← The application (open this in your browser)
├── LICENSE.txt                     ← MIT Licence
├── README.md                       ← This file
├── CITATION.cff                    ← Machine-readable citation metadata
├── docs/
│   ├── EBS_User_Guide_v8_0_3.docx                        ← Complete user guide and technical reference
│   └── Math_used_in_the_molecular_solver_code_v8_0_3.docx ← Mathematical derivations for the solver code
└── paper/                          ← Journal paper source files
```

---

## Validation

Validated across a 20-molecule test suite spanning:

* Targets **within** the convex hull of sources → SSR ≤ 10⁻¹⁰ (exact recovery confirmed)
* Targets **outside** the hull → minimum-SSR approximation with physically valid fractions
* Sum constraint ‖Σfᵢ − 1‖ < 10⁻⁸ across all test cases

Full validation details, including 232 automated tests and 12 interactive acceptance tests, are documented in Section 13 of the user guide.

---

## Citation

If you use EBS in your research, please cite it. A `Cite this repository` button is available at the top of this page (powered by `CITATION.cff`).

**Software archive (cite this for the code itself):**

> Mason, P. (2026). *Elemental Blend Solver (EBS)* (Version 8.0.3) [Software]. Zenodo. <https://doi.org/10.5281/zenodo.19075263>

The following journal papers are currently under review. This section will be updated with full references and DOIs upon acceptance:

> **Mason, P.** (forthcoming). Elemental Blend Solver: A Browser-Native Non-Negative Least Squares Framework for Polymer Elemental Composition Reconstruction. *Chemometrics and Intelligent Laboratory Systems.*

> **Mason, P.** (forthcoming). Elemental Blend Solver (EBS): A Browser-Based Non-Negative Least Squares Tool for Polymer Elemental Composition Matching. *Journal of Open Source Software.*

---

## Licence

MIT — see [LICENSE.txt](LICENSE.txt).  
Copyright © 2026 Paul Mason.

---

## Contact

**Paul Mason** — Independent Researcher  
📧 [pemason437@gmail.com](mailto:pemason437@gmail.com)  
🔗 <https://github.com/pemason437-svg/Elemental-Blend-Solver>

Bug reports and feature requests are welcome via the [issue tracker](https://github.com/pemason437-svg/Elemental-Blend-Solver/issues).
