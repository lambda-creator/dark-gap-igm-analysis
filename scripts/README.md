# Scripts

This directory contains the full pipeline for **dark gap analysis in the Lyman-$\alpha$ forest**, based on synthetic sightlines generated from EX-CITE simulations.

The workflow spans:

1. Optical depth generation
2. Dark gap identification
3. Statistical analysis (CDF, $F_{10}$, PDF)
4. Parameter inference ($\chi^2$ minimization)
5. Mean free path estimation

---

## Pipeline Overview

The analysis pipeline proceeds as follows:

```text
Optical Depth → Flux → Dark Gap Identification → Statistical Analysis → Parameter Constraints
```

---

## Script Descriptions

### 1. `lyalpha_optical_depth_code.py`

* Generates **Lyman-$\alpha$ optical depth ($\tau$)** along sightlines using simulation outputs.
* Includes:

  * Density, velocity, and redshift fields
  * Photoionization rate fluctuations
  * Voigt profile computation
* Uses **MPI parallelization** for efficient computation.

👉 Output: Synthetic spectra ($F = e^{-\tau}$)



---

### 2. `dark_gap_identification.py`

* Core pipeline for **identifying dark gaps** from synthetic spectra.
* Steps include:

  * Instrumental convolution (XQR-30 resolution)
  * Rebinning to $1,h^{-1},\mathrm{Mpc}$
  * Noise addition (based on observational SNR)
  * Thresholding ($F < 0.05$)
  * Binary closing to remove spurious spikes
  * Extraction of contiguous dark regions

👉 Output: Dark gap catalog (start, end, length)



---

### 3. `CDF.py`

* Computes the **Cumulative Distribution Function (CDF)** of dark gap lengths.
* Compares simulation outputs with observations (Zhu et al. 2021).
* Includes:

  * KS-test statistics ($D$, $p$ values)
  * Distribution of p-values across mocks

👉 Purpose: Diagnostic comparison between models and observations



---

### 4. `F10_statistics.py`

* Computes the **$F_{10}$ statistic**, defined as the CDF of gaps with length $> 10,h^{-1},\mathrm{Mpc}$.
* Used as a **consistency check** for model behavior.

👉 Insight: Sensitive to large neutral regions in the IGM



---

### 5. `PDF+chi2.py`

* Computes **probability distribution functions (PDFs)** of dark gap lengths using KDE.
* Performs:

  * KDE estimation (adaptive bandwidth)
  * Masking of low-probability regions
  * $\chi^2$ computation against observational PDFs
  * Interpolation over $\langle \Gamma_{\mathrm{HI}} \rangle$

👉 Output: $\chi^2$ tables and best-fit photoionization rates



---

### 6. `true_mfp.py`

* Computes the **true mean free path ($\lambda_{\mathrm{mfp}}$)** of ionizing photons.
* Steps:

  * Generate LyC optical depth
  * Compute transmitted flux
  * Stack sightlines
  * Fit exponential decay:
    $$
    F(x) = F_0 \exp(-x / \lambda_{\mathrm{mfp}})
    $$

👉 Uses MPI for large-scale computation



---

### 7. `fitted_LyC_flux.py`

* Performs **post-processing and visualization** of LyC flux profiles.
* Fits exponential model to extract $\lambda_{\mathrm{mfp}}$.

👉 Purpose: Validation and sanity check of mean free path estimation



---

## Execution Order

To reproduce the full analysis:

```text
1. lyalpha_optical_depth_code.py
2. dark_gap_identification.py
3. CDF.py
4. F10_statistics.py
5. PDF+chi2.py
6. true_mfp.py
7. fitted_LyC_flux.py
```

---

## Key Parameters

* Flux threshold: $F < 0.05$
* Minimum gap length: $1,h^{-1},\mathrm{Mpc}$
* Redshift range: $5.0 < z < 6.0$
* Models: Early, Late (Fiducial), Ultra-Late reionization
* $\log \Gamma_{\mathrm{HI}}$ scaling explored

---

## Notes

* Scripts assume specific directory structures for simulation outputs.
* Large datasets are not included; paths may need to be modified.
* MPI-based scripts (`lyalpha_optical_depth_code.py`, `true_mfp.py`) require parallel execution.

---

## Summary

This directory provides a **complete and modular framework** for analyzing dark gap statistics in the Lyman-$\alpha$ forest, enabling:

* Model comparison with observations
* Statistical characterization of neutral regions
* Constraints on the ionization state of the IGM
* Estimation of the mean free path of ionizing photons
