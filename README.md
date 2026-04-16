# Shock Function Alternatives for GBM Stock Prediction Models

## Overview

This project investigates whether the standard normal inverse — the near-universal shock function in Geometric Brownian Motion (GBM) stock models — can be replaced with mathematically grounded alternatives that offer better predictive performance.

Two candidate functions are derived from first principles:
- A **logistic inverse**: `ln(x / 1 - x)`, derived as the inverse CDF of the logistic distribution
- A **scaled tangent**: `γ · tan(πx − π/2)`, whose inverse derivative yields the Lorentz (Cauchy) distribution

All three shock functions are implemented in Excel with a 500-run Monte Carlo simulation across 11-day prediction horizons for MSFT and TSLA, using the GBM discretisation:

$$S_{t+1} = S_t \cdot \exp\left[\left(\mu - \frac{\sigma^2}{2}\right)\Delta t + \sigma\sqrt{\Delta t}\, Z\right]$$

where $Z$ is the output of each shock function applied to `RAND()`.

---

## Key Findings

| Shock Function | MSFT Mean Error | MSFT Std Dev | TSLA Mean Error | TSLA Std Dev |
|---|---|---|---|---|
| `norm.s.inv` (baseline) | ~3.5% | ~2.0% | ~7.5% | ~4.5% |
| `ln(x / 1-x)` | ~3.5% | ~1.4% | ~8.3% | ~3.2% |
| `γ · tan(πx − π/2)` | ~2.5–2.7%* | ~1.0–8.5%* | ~2.5%* | ~2.0–5.0%* |

*When stable — approximately 50% of runs produce extreme divergence (up to 10⁶% error).

- The **ln variant** matches the baseline in mean error but consistently produces tighter error bands, making it a viable drop-in replacement
- The **tan function** is theoretically interesting but practically unusable — a direct consequence of the Cauchy distribution having undefined mean and variance
- Notably, setting γ = 0 (drift-only, no shock) minimises mean error and reduces std dev to 0, suggesting that for stable blue-chips over short horizons, directional uncertainty dominates shock modelling

---

## Files

| File | Description |
|---|---|
| `GBM_shock_functions.xlsx` | Full Excel model with Monte Carlo simulation, graphs, and all three shock functions |
| `Testing different shock functions.pdf` | Written report covering derivations, methodology, results, and discussion |

---

## How to Use

1. Open `GBM_shock_functions.xlsx` in Excel (requires `STOCKHISTORY()` function — available with a Microsoft 365 account)
2. Enter a stock ticker in cell **A1** (NYSE tickers work directly; for other exchanges use format `XASX:BHP` with relevant exchange code)
3. Set γ in cell **U1** (recommended: `0.005` for the tan function)
4. Go to **Formulas → Calculation → Calculate Sheet** to run all 500 Monte Carlo simulations
5. Results and graphs update automatically

---

## Mathematical Background

### Logistic inverse
The function `f(x) = eˣ / (eˣ + 1)²` is shown to be a valid PDF (integrates to 1, is non-negative). Its CDF is `F(x) = eˣ / (eˣ + 1)`, and its inverse CDF — used as the shock function — is `ln(x / 1 - x)`.

### Tangent / Lorentz
The inverse CDF `y = γ · tan(πx − π/2)` corresponds to a Cauchy (Lorentz) distribution with scale parameter γ. Its PDF `γ / π(γ² + x²)` is valid, but the distribution has no finite moments — explaining the tan function's extreme instability in practice.

---

## Related Work

This project builds on a prior paper published in *Parabola* (UNSW): [Weighting for a Better Distribution — Gram-Charlier in the World of Finance]([https://parabola.unsw.edu.au](https://drive.google.com/file/d/1tf-y3Yc5dop67EpqQZbDeXW_f6YClDhS/view)), which derives an alternative Gram-Charlier PDF to model asymmetric return distributions more accurately than the standard normal assumption.

---

## Limitations & Future Work

- Results are sensitive to the specific 11-day window tested; a rolling or expanding window evaluation would be more robust
- Only two stocks tested — broader coverage across sectors and volatility regimes would strengthen conclusions
- The Gram-Charlier inverse CDF (from the related paper) remains an open avenue for use as a shock function
- A truncated or clipped version of the tan function could isolate its shape properties from its infinite-variance pathology

---

## Author

**Advik Sakhare** — B.Actuarial Studies/B.Advanced Math(Hons), Firt year  
April 2026
