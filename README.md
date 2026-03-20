# Elemental Blend Solver (EBS) v9.0.0

**A browser-native, zero-install NNLS solver for polymer elemental composition matching.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://github.com/pemason437-svg/Elemental-Blend-Solver/blob/main/LICENSE.txt)
[![Version](https://img.shields.io/badge/version-9.0.0-blue.svg)](https://github.com/pemason437-svg/Elemental-Blend-Solver)
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

1. Download **`molecular_solver_v9_0_0.html`** to any folder on your computer
2. Double-click it to open in your browser

That's it. No installation, no npm, no Python, no internet connection required. The entire application — solver, database, charts, and export — is contained in the single HTML file.

**Tested browsers:** Chrome ≥ 80, Edge ≥ 80, Firefox ≥ 78, Safari ≥ 14

---

## What's New in v9.0.0

### Bug fixes
- **DGEBA formula corrected** — `C21H25ClO5` → `C21H24O4` (CAS 1675-54-3, MW 340.41). The old formula introduced a spurious 9% chlorine, causing DGEBA to appear as a Cl-supplying polymer in blends.
- **Phenol-Formaldehyde formula corrected** — `C7H6O2` → `C7H6O` (novolac repeat unit −C₆H₃(OH)−CH₂−). Corrects O% from 26.2% to 15.1%.
- **Se measurement uncertainty corrected** — σ(Se) fixed from 0.005 → 0.004 wt-fraction units, consistent with the IUPAC combustion-analyser grouping stated in the companion paper.
- **Leave-One-Out classification thresholds aligned** — Essential / Useful / Redundant labels now correctly implement the ×10 SSR factor defined in the companion paper (>10× / 1–10× / <1×), replacing the earlier >10% / 1–10% / <1% thresholds.

### Graphical analysis — major overhaul (all 7 charts)

**Radar chart — publication-quality redesign**
- Target polygon recoloured to fixed navy (#0D1B5E) matching Figure 2 of the companion paper; best-fit blend changed to deeper purple (#9E2FB5).
- Source polygons now rendered as dashed outlines only (no fill), using a distinct per-source scientific colour palette — eliminates visual clutter from overlapping fills.
- Vertex dot markers added to both target and blend polygons for precise axis-intersection reading.
- Legend moved to horizontally centred position at the bottom.
- Canvas enlarged from 400×400 to 460×420 px for improved label spacing.

**PCA scatter — completely rebuilt around the full 73-polymer database**
- All 73 database polymers now plotted as grey hollow open circles, giving immediate visual context for where the target sits in the full polymer composition space — not just relative to the 5 chosen sources.
- PCA axes are now computed from the entire database (not just the active sources), so axis orientation is stable as sources are swapped in and out.
- Annotation box added (top-left) explicitly stating whether the target lies outside the active-source convex hull.
- Legend box added (bottom-right) identifying the DB-polymer cloud and the best-fit blend dot.
- The redundant all-sources faint-dashed hull has been removed; only the active-source hull is shown.
- Canvas enlarged from 380×310 to 480×360 px.

**Monte Carlo sensitivity — SSR histogram overhauled**
- Histogram bars are now coloured green / gold / red by statistical position (below P5 / within P5–P95 / above P95) rather than by arbitrary thirds, making the distribution shape immediately interpretable.
- Histogram height increased from 60 px to 180 px for legibility.
- P5 and P95 boundaries are now visually demarcated.
- Confidence-interval strips redesigned to match Figure 5 of the companion paper: horizontal 0–100% axis, per-source colour coding, 90% CI wide bar, IQR narrow bar, and mean vertical line, with a stability label per source.

**New 7th chart tab: Composition (target vs achieved bar chart)**
- Side-by-side grouped bars comparing target elemental weight fractions (blue) against the best-fit blend (orange) for each active element.
- Foreign-element annotation arrow with explicit Δ (pp) value is displayed when any target element is absent from all sources — matching the Figure 7 diagnostic style from the companion paper.

---

## Features

### Solver

* Non-Negative Least Squares via Lawson–Hanson active-set algorithm
* Up to 5 source polymers simultaneously
* **20 tracked elements:** C, H, O, N, Cl, S, P, Si, F, Br, I, Na, K, Mg, Ca, Fe, Al, B, Se, Zn
* **73-polymer database** covering commodity thermoplastics, engineering polymers, thermosets, and specialty materials
* 8 target presets + custom molecular formula entry
* Real-time results — updates instantly as you change any input

### Graphical Analysis (7 views)

| View | Description |
| --- | --- |
| Radar Chart | Target, best-fit blend, and per-source elemental profiles on radial axes |
| PCA Scatter | All 73 database polymers + active sources + target in 2D PCA space |
| Build-Up Chart | Per-source contribution to each element |
| Blend Error Chart | Signed per-element residuals (achieved − target) |
| Feasibility Map | Two-dimensional convex-hull cross-section |
| Convergence Plot | Illustrative projected-gradient descent (not the actual solver path) |
| Composition | Side-by-side target vs achieved bar chart with foreign-element annotation |

### Advanced Diagnostic Tools (8 tools)

| Tool | Description |
| --- | --- |
| Monte Carlo Sensitivity | Propagates source-composition uncertainty to mixing-fraction confidence intervals (2,000 replicates, σ = 2% multiplicative noise) |
| Distance-to-Hull | L₂ distance from target to nearest point on source convex hull |
| Substitution Recommender | Scans all 73 database polymers for single-polymer replacements that reduce SSR |
| Leave-One-Out Importance | SSR increase when each active source is removed (Essential >10× / Useful 1–10× / Redundant <1×) |
| Global Decomposability Score | Dimensionless [0, 1] accept/reject metric: GDS = max(0, 1 − RMSE/‖**b**‖₂). GDS ≥ 0.97 excellent; 0.92–0.97 acceptable; 0.80–0.92 marginal; < 0.80 poor |
| PCA Database Coverage Map | All 73 polymers + target projected onto 2D PCA space with convex hull of active sources |
| Ternary SSR Contour | Heat-map of the error surface over the full 3-source simplex (3-source mode) |
| Exhaustive Optimal Blend Search | Enumerates all eligible combinations to find the globally optimal source set |

### Export

* **Word Report (.docx)** — 8-section formal report with all charts embedded as SVG
* **SVG Charts** — all 7 graphical views as standalone SVG files
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
| **[EBS\_User\_Guide\_v9\_0\_0.docx](docs/EBS_User_Guide_v9_0_0.docx)** | Complete technical reference and user guide. Covers quick-start, all UI features, full mathematical framework with annotated JavaScript code listings, advanced tool documentation (including corrected χ² df = m − n_active and GDS formula), troubleshooting, glossary, and software verification report. |
| **[Math\_used\_in\_the\_molecular\_solver\_code\_v9\_0\_0.docx](docs/Math_used_in_the_molecular_solver_code_v9_0_0.docx)** | Detailed mathematical derivations underpinning the solver code. Covers the NNLS formulation, Lawson–Hanson active-set algorithm, sum constraint penalty, corrected chi-squared degrees of freedom (df = m − n_active), Global Decomposability Score (GDS = max(0, 1 − RMSE/‖b‖₂)), and all supporting calculations used in the implementation. |

---

## Repository Structure

```
Elemental-Blend-Solver/
├── molecular_solver_v9_0_0.html    ← The application (open this in your browser)
├── LICENSE.txt                     ← MIT Licence
├── README.md                       ← This file
├── CITATION.cff                    ← Machine-readable citation metadata
├── docs/
│   ├── EBS_User_Guide_v9_0_0.docx                        ← Complete user guide and technical reference
│   └── Math_used_in_the_molecular_solver_code_v9_0_0.docx ← Mathematical derivations for the solver code
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

> Mason, P. (2026). *Elemental Blend Solver (EBS)* (Version 9.0.0) [Software]. Zenodo. <https://doi.org/10.5281/zenodo.19075263>

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
