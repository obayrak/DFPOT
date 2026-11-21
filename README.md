# Double Folding Potential Lab

A single-file, browser-based double folding model (DFM) calculator for the real part of the nucleus–nucleus optical potential, for **spherical and axially deformed nuclei**. It computes the potential from the ground-state densities of both nuclei and an effective M3Y nucleon–nucleon interaction, explains the formalism, and benchmarks the results against BiFold, DFPOT, closed-form solutions and a brute-force 6-D reference.

Everything (solver, plots, theory, benchmarks) is in one HTML file. There is nothing to install or build.



## Features

### Spherical nuclei (Calculator tab)
- **Densities:** Gaussian, harmonic oscillator, 2pF, 3pF, 3pG, symmetrised Fermi, BiFold/DFPOT 3pF, São Paulo systematics, or tabulated densities (RIPL-3 HFB `r ρn ρp` tables, BiFold formats, multi-nucleus files). All are normalised to A.
- **NN interaction:** M3Y-Reid and M3Y-Paris, with density dependence DDM3Y1, BDM3Y1–3 and CDM3Y1–6 (Khoa et al.), and the energy factor g(E).
- **Exchange:** zero range (Ĵ₀₀), or finite range in the local-momentum approximation solved self-consistently per R (safeguarded secant), with an extended Thomas–Fermi k_F (C_S = 1/4 or 1/36).
- **Coulomb:** uniformly charged spheres, or double-folded charge densities (Z/A)ρ.
- **Numerical methods:** Fourier–Bessel (momentum space) with Gauss–Legendre, Simpson or Filon quadrature, and an independent coordinate-space (bipolar) method.
- **Derived quantities:** volume integral J_V/(A_P A_T), rms radius, s-wave barrier (V_B, R_B, ħω), Woods–Saxon fit, and a mesh-convergence study with error estimates.
- **Output:** potential tables, `fort.4` for FRESCO (SHAPE = 7) and numerical-potential blocks for ECIS.

### Deformed nuclei (Deformed densities and Deformed calculator tabs)
- **Axially deformed shapes:** deformed 2pF R(θ) = R₀[1 + Σ β_λ Y_λ0], deformed Gaussian, anisotropic oscillator, any spherical model with R → R(θ), or tabulated multipoles ρ_λ(r).
- **Normalisation options:** fix ρ₀ or keep the volume. A centre-of-mass correction (β₁) is applied automatically for shapes with odd β_λ.
- **Multipole projection:** adaptive λ_max with an error monitor; intrinsic moments Q_λ, Q₀, effective β_λ.
- **Multipole folding:** U(R; θ_P, θ_T, φ) = Σ U_{λPλT;L}(R) A_{λPλT;L}(θ_P, θ_T, φ). The radial terms are folded once, so any orientation is evaluated instantly.
- **Density dependence** is applied to ρ(r, θ′) point by point and then projected (no linearisation).
- **Exchange:** zero range, or finite range with a deformed local k_F(r, θ′) (full 3-D Laplacian, or the radial convention of the spherical solver).
- **Coulomb:** orientation-dependent double-folded multipoles.
- **Results:** orientation scans (tip/side), barrier height and radius against angle, orientation averages, and coupled-channel rotor form factors F_k(R) for FRESCO (TYPE 10/11) and ECIS.

### Numerics and Benchmark tabs
- Convergence on the analytic α + α case.
- Comparisons with BiFold and DFPOT for α + ⁴⁰Ca (M3Y and CDM3Y6) and ¹⁶O + ¹⁶O (DDM3Y1).
- **Deformed Case 5:** anisotropic Gaussians against the closed form at 392 orientations (agreement about 10⁻¹¹).
- **Deformed Case 6:** ²⁴Mg-like + ¹⁵⁴Sm-like with CDM3Y6, against a Cartesian FFT cubature reference.
- Every benchmark can be re-run in the browser and compared with the stored values.

Every plot and table has an export button (`.dat` space-aligned, or tab-separated `.txt`).

## Using the solver from JavaScript

The physics engine (`DFM.js`) is embedded in the page in `<script id="dfm-src">` and exposed as `window.DFM`. In the browser console:

```js
const P = DFM.makeDensity({ model: 'gauss',  p: { rho0: 1, a: 1.19 },        A: 4  });
const T = DFM.makeDensity({ model: 'fermi2', p: { rho0: 1, R: 3.6, a: 0.52 }, A: 40 });
const res = DFM.solve({ rhoP: P, rhoT: T, vnn: 'paris', dd: 'cdm3y6', exchange: 'fr',
                        Elab: 104, AP: 4, AT: 40, ZP: 2, ZT: 20, coul: 'fold' });
console.log(res.R, res.nuclear, res.coulomb);
```

The same code runs under Node.js: copy the contents of the `dfm-src` script into `dfm.js` and `require('./dfm.js')`. The main entry points are `solve`, `solveCoord`, `makeDefDensity`, `projectDensity`, `solveDef`, `evalOrient`, `orientAverage`, `exchangeFRDef`, `evalOrientFR`, `ccFormFactors` and `benchDef`. Units are fm and MeV throughout.

## Conventions and limitations

- Y_λμ use the Condon–Shortley phase and are normalised to 1 on the unit sphere. The body-frame symmetry axis is z′, and R points from target to projectile.
- In the spherical solver, k_F uses ρ″ in place of ∇²ρ, to match BiFold. The deformed solver uses the full Laplacian by default and offers both.
- The real potential has no renormalisation factor N_R and no dynamic polarisation potential. Fit those against scattering data.
- With both nuclei deformed and finite-range exchange, the exported F_k use κ(R) from the monopole problem. Their accuracy is best near and outside the barrier (see the Theory tab).
- The FRESCO and ECIS export layouts for coupled-channel form factors have **not been verified against actual FRESCO or ECIS runs**. Check the reading order and normalisation in your code's manual (a simple test: the 0⁺ → 2⁺ coupling should equal F₂(R)).
- **Fetch from RIPL-3** works only when the page is opened inside the Claude desktop app, which provides a browser. Elsewhere, open the RIPL-3 page with the link and paste or upload the table.

## Dependencies

Loaded from CDNs at run time:

- [Chart.js](https://www.chartjs.org/) 4.4.1 (MIT): plots
- [MathJax](https://www.mathjax.org/) 3.2.2 (Apache-2.0): equations
- Google Fonts: Source Serif 4, IBM Plex Sans, IBM Plex Mono (SIL Open Font License)

Without these the calculators still run, but plots and equations do not render.

## References

- M. Karakoç, *BiFold: double folded potentials with density-dependent NN interactions*, Comput. Phys. Commun. **279** (2022) 108613. <https://github.com/mkarakoc/BiFold>
- J. Cook, *DFPOT – a program for the calculation of double folded potentials*, Comput. Phys. Commun. **25** (1982) 125.
- R. Capote et al., *RIPL – Reference Input Parameter Library*, Nucl. Data Sheets **110** (2009) 3107. <https://www-nds.iaea.org/RIPL-3/>
- G. R. Satchler and W. G. Love, Phys. Rep. **55** (1979) 183.
- G. Bertsch, J. Borysowicz, H. McManus and W. G. Love, Nucl. Phys. A **284** (1977) 399.
- N. Anantaraman, H. Toki and G. F. Bertsch, Nucl. Phys. A **398** (1983) 269.
- D. T. Khoa and W. von Oertzen, Phys. Lett. B **342** (1995) 6.
- D. T. Khoa, G. R. Satchler and W. von Oertzen, Phys. Rev. C **56** (1997) 954.
- D. T. Khoa, W. von Oertzen and H. G. Bohlen, Phys. Rev. C **49** (1994) 1652.
- D. T. Khoa and G. R. Satchler, Nucl. Phys. A **668** (2000) 3.
- A. M. Kobos, B. A. Brown, R. Lindsay and G. R. Satchler, Nucl. Phys. A **384** (1982) 65.
- W. G. Love and L. W. Owen, Nucl. Phys. A **239** (1975) 74.
- H. De Vries, C. W. De Jager and C. De Vries, At. Data Nucl. Data Tables **36** (1987) 495.
- L. C. Chamon et al., Phys. Rev. C **66** (2002) 014610.
- B. Buck, H. Friedrich and C. Wheatley, Nucl. Phys. A **275** (1977) 246.
- X. Campi and A. Bouyssy, Phys. Lett. B **73** (1978) 263.
- M. Abramowitz and I. A. Stegun, *Handbook of Mathematical Functions*, §25.4.47.

## Citing

If you use this code in published work, please cite this repository and the original folding-model references above. A `CITATION.cff` file can be added for GitHub's "Cite this repository" button.

## License

Released under the [MIT License](LICENSE). Copyright (c) 2026 [Orhan Bayrak].
