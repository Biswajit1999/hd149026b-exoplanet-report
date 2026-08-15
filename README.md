# HD 149026 b — Real TESS Transit Report

<p align="center">
  <img src="figures/hd149026b_tess_transit.png" alt="Phase-folded real TESS transit light curve of HD 149026 b" width="760">
</p>

One real public TESS SPOC light curve; one historical NASA Exoplanet Archive ephemeris; one timing-adjusted, limb-darkened transit fit.

**[Open the full report](https://biswajit1999.github.io/hd149026b-exoplanet-report/)** — the live GitHub Pages version.

## Data sources

- **System parameters** — the saved `pscomppars` row from the [NASA Exoplanet Archive TAP service](https://exoplanetarchive.ipac.caltech.edu/TAP/sync?query=select+pl_name%2Chostname%2Cra%2Cdec%2Cpl_orbper%2Cpl_tranmid%2Cpl_trandur%2Cpl_rade%2Cpl_bmasse%2Cpl_eqt%2Cpl_orbsmax%2Csy_dist%2Csy_tmag%2Cst_teff%2Cst_rad%2Cst_mass%2Cdisc_year%2Cdiscoverymethod%2Cdisc_refname%2Cdisc_pubdate%2Cdisc_facility+from+pscomppars+where+pl_name%3D%27HD+149026+b%27&format=csv).
- **Observed photometry** — unmodified MAST file `tess2020106103520-s0024-0000000255930614-0180-s_lc.fits`, TESS Sector 24, DOI [10.17909/t9-nmc8-f686](https://doi.org/10.17909/t9-nmc8-f686). This is a real SPOC reduced light curve, not simulated data.
- Exact URLs, IDs, retrieval date, and SHA-256 checksum are in [`data/SOURCE.md`](data/SOURCE.md).

## Reproduce the analysis

```bash
pip install -r requirements.txt
python scripts/analyze_transit.py
python scripts/analyze_multisector.py
pytest tests/ -v
```

The script keeps finite `QUALITY == 0` cadences, normalizes `PDCSAP_FLUX`, and applies one symmetric robust outlier rule. A local linear null is compared with a circular quadratic-limb-darkened transit. The archive period and predicted phase are retained, while midpoint, radius ratio, impact parameter, baseline, and baseline slope are fitted inside a bounded window. The limb-darkening coefficients and scaled semi-major axis are fixed and disclosed in the CSV.

## What the corrected fit shows

| Quantity | Result |
|---|---:|
| TESS sector | 24 |
| Cadences in fitted window | 3577 |
| Transit support | ΔBIC ≥ 10 |
| Midpoint correction | -0.023 h ± 0.24 min |
| Model mid-transit depth | 2925.3 ± 18.8 ppm |
| Radius ratio Rp/Rs | 0.05066 |
| Fitted / published duration | 3.231 / 3.140 h |
| Linear null χ² / dof / BIC | 42136.98 / 3575 / 42153.35 |
| Transit χ² / dof / BIC | 5241.64 / 3572 / 5282.55 |
| ΔBIC (null − transit) | 36870.80 |

The timing-adjusted transit is strongly preferred by ΔBIC = 36870.8. Its fitted midpoint is -0.023 hours from the historical prediction; the model's mid-transit depth is 2925.3 ± 18.8 ppm. A fitted timing correction can diagnose ephemeris drift, but this single-sector fit is not a replacement for a global transit-timing analysis.

<!-- MULTISECTOR-UPGRADE-START -->
## Multi-sector robustness and correlated noise

The archive prediction was timing-adjusted independently in 3 fitted sector(s) (S24, S25, S52), of which 3 meet Delta BIC >= 10. Formal depth errors were inflated by sqrt(max(reduced chi-square, 1)) times the residual time-averaging beta factor (observed range 1.75-3.12). The robust inverse-variance model depth across supported sectors is 2915.2 +/- 23.4 ppm; Cochran Q = 0.20 for 2 dof (p = 0.9069). These scaled errors address underestimated scatter and short-timescale correlation, but they are not a full Gaussian-process or physical limb-darkened transit fit.

<p align="center"><img src="figures/hd149026b_multisector_transits.png" alt="Independent sector transit fits for HD 149026 b" width="760"></p>

<p align="center"><img src="figures/hd149026b_depth_consistency.png" alt="Sector depth consistency for HD 149026 b" width="760"></p>

<p align="center"><img src="figures/hd149026b_noise_diagnostics.png" alt="Residual RMS time-averaging diagnostic for HD 149026 b" width="760"></p>

The per-sector table is in [`figures/multisector_statistics.csv`](figures/multisector_statistics.csv). Regenerate all three figures with `python scripts/analyze_multisector.py`.
<!-- MULTISECTOR-UPGRADE-END -->

## System context

- Radius: 8.29 Earth radii
- Mass: 120.78 Earth masses
- Orbital period: 2.875890 days
- Transit duration: 3.140 hours
- Semi-major axis: 0.0436 AU
- Equilibrium temperature: 1626 K
- Host: HD 149026 · distance 75.86 pc
- Discovery: 2005 by Radial Velocity (Subaru Telescope)

## Limitations

- The orbit is assumed circular and the quadratic limb-darkening coefficients are fixed representative values; they are not atmosphere-grid interpolations.
- The scaled semi-major axis is derived from the saved composite semi-major axis and stellar radius; their uncertainties are not propagated.
- Midpoint freedom corrects accumulated ephemeris error but introduces a bounded timing search. ΔBIC, not a naïve one-parameter p-value, is used as the support gate.
- PDCSAP processing, dilution, stellar variability, transit-timing variations, and long-timescale covariance can still bias the inferred geometry.
- Radius ratio, impact parameter, and fixed limb darkening are correlated. Published global fits with physical priors and simultaneous detrending remain authoritative.

## Repository structure

```text
README.md
index.html
requirements.txt
data/                       unmodified TESS FITS + NASA row + SOURCE.md
scripts/analyze_transit.py  timing-adjusted limb-darkened transit fit
figures/                    generated plot + summary_statistics.csv
tests/                      real-data regression tests
.github/workflows/tests.yml CI on every push and pull request
LICENSE                     MIT
```

## References

1. [Sato et al. 2005](https://ui.adsabs.harvard.edu/abs/2005ApJ...633..465S/abstract) — discovery reference as listed by the NASA Exoplanet Archive.
2. Ricker, G. R. et al. (2015), *Transiting Exoplanet Survey Satellite (TESS)*, JATIS 1, 014003, [doi:10.1117/1.JATIS.1.1.014003](https://doi.org/10.1117/1.JATIS.1.1.014003).
3. TESS Team, *TESS Light Curves — All Sectors*, MAST, [doi:10.17909/t9-nmc8-f686](https://doi.org/10.17909/t9-nmc8-f686); Sector 24 used here.
4. [NASA Exoplanet Archive](https://exoplanetarchive.ipac.caltech.edu/), `pscomppars` TAP row retrieved 2026-08-15.

## Author

Biswajit Jana — [Portfolio](https://biswajit1999.github.io/Biswajit_Jana.github.io/) · [GitHub](https://github.com/Biswajit1999) · [LinkedIn](https://www.linkedin.com/in/biswajit-jana-27011a151/) · [ORCID](https://orcid.org/0009-0002-2411-1891)
