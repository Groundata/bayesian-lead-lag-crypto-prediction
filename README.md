# Bayesian Lead–Lag Modeling Between Bitcoin Spot and a Prediction Market

## Overview

This project investigates whether short-term movements in one financial market can predict future movements in another. Specifically, I study:

- **Market A:** Bitcoin spot price  
- **Market B:** Kalshi prediction market — *“Will Bitcoin cross $100k again this year?”*

The central research question is:

> Do current moves in Market A predict future moves in Market B?  
> If so, is this lead–lag relationship economically exploitable?

To answer this, I construct a Bayesian time-series model and estimate parameters using MCMC sampling.

---            

## Motivation

Markets that reference the same underlying asset should be related. However, due to differences in liquidity, volume, and information processing speed, one market may react faster than another.

Bitcoin spot markets are highly liquid and continuously traded.  
Prediction markets, while informative, often exhibit lower liquidity and slower price adjustment.

If:

\[
E[\Delta p_B | \Delta p_A] \neq 0
\]

then:
- The markets are statistically linked
- The relationship can be quantified
- We may detect a measurable lead–lag structure

Such structure could inform trading strategies, hedging decisions, or risk management.

---

## Model Specification

### Notation

- \( p_a(t) \): Price of Bitcoin at time \( t \)
- \( p_b(t) \): Price of Kalshi market at time \( t \)
- \( x_t = \Delta p_a(t) \): Change in Bitcoin price
- \( y_t = \Delta p_b(t) \): Change in Kalshi price
- \( a \): Drift term
- \( r \): Lead–lag coefficient (impact of Bitcoin on future Kalshi)
- \( z \): Autoregressive coefficient for Kalshi
- \( \sigma^2 \): Noise variance
- \( \epsilon_t \sim N(0, \sigma^2) \)

---

### Fitting Function

I model the conditional expectation:

\[
\mu_{t+\Delta} = a + r x_t + z y_t
\]

Adding Gaussian noise gives the full model:

\[
y_{t+\Delta} = a + r x_t + z y_t + \epsilon_{t+\Delta}
\]

This is a linear autoregressive model with a cross-market lag term.

---

## Bayesian Framework

### Priors

Relatively weak priors were used:

- \( a \sim N(0,1) \)
- \( r \sim N(0,1) \)
- \( z \sim N(0,1) \)
- \( \sigma \sim \text{HalfNormal}(1) \)

These priors allow the data to dominate posterior inference.

---

### Likelihood

\[
y_{t+\Delta} \sim N(\mu_{t+\Delta}, \sigma^2)
\]

Posterior distributions were estimated using MCMC sampling:
- 4 chains
- 10,000 draws
- 2,000 burn-in

Trace plots confirmed good mixing and convergence.

---

## Data

Bitcoin data:
https://www.kaggle.com/datasets/mczielinski/bitcoin-historical-data

Kalshi data:
Fetched via Kalshi public API.

Due to file size constraints, raw data files are not included in this repository.  
The notebook contains preprocessing steps to align and construct the modeling dataset.

### Preprocessing Steps

- Extract minute-level price data
- Align timestamps across markets
- Forward-fill missing Kalshi entries
- Convert Kalshi price from cents to dollars
- Compute first differences
- Standardize variables for numerical stability

---

## Results

### Key Posterior Findings

**1. Lead–Lag Coefficient (r)**  
- Posterior mean ≈ 0.02 (standardized)
- 94% HDI excludes zero

Interpretation:
There is statistical evidence of a positive lead–lag relationship. Bitcoin moves tend to be followed by Kalshi moves in the same direction.

---

**2. Autoregressive Term (z)**  
- Posterior mean ≈ −0.21  
- Mostly negative mass

Interpretation:
Short-term mean reversion exists in the Kalshi market.

---

**3. Noise (σ)**  
- Posterior concentrated near 1 (standardized)

Interpretation:
While relationships are statistically detectable, most variance remains unexplained.

---

## Economic Significance

When unstandardized:

- A 1% Bitcoin move (~$1,000 near $100k BTC)
- Predicts ≈ 0.26 percentage point change in Kalshi price

Although statistically significant, the magnitude is small relative to:
- Bid–ask spreads
- Liquidity constraints
- Execution latency
- Market microstructure noise

Conclusion:
The lead–lag effect is real but likely not economically exploitable in its current form.

---

## Limitations

- Linear approximation
- Constant lag assumption
- No regime dependence
- No explicit modeling of time-to-expiry effects
- No structural microstructure modeling

---

## Future Extensions

- Introduce time-to-expiration parameter
- Compare Bayesian inference to OLS / VAR / Granger causality
- Test regime-switching models
- Backtest paper trading strategy
- Apply framework to other prediction markets

---

## Tools Used

- Python
- Pandas
- NumPy
- MCMC sampling (PyMC / similar)
- Matplotlib
- Kalshi API
- Kaggle data

---

## Takeaways

This project demonstrates how Bayesian inference can be used to:

- Detect cross-market predictive structure
- Quantify statistical significance
- Distinguish statistical vs economic significance
- Evaluate practical exploitability

While the detected lead–lag relationship is statistically meaningful, it appears too small relative to noise and market frictions to generate robust alpha.

---

## Author

Freddy Meier  
Applied Mathematics & Statistics  
Florida State University
