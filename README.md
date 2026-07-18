[README.md](https://github.com/user-attachments/files/30150516/README.md)
# Climate Risks in Life Insurance
### A Comparative Analysis of Mortality and Actuarial Modeling among European Countries under Solvency II

**Author:** Kamal El Halfaoui — MSc in Finance and Risk Management, Università degli Studi di Firenze
**Supervisor:** Prof. Ilaria Colivicchi
**Panel:** Italy · France · Germany · Netherlands | 1990–2024

---

## Introduction

The most important assumption behind insurance pricing is that mortality is stable — that the recent past predicts the future. **Climate change breaks that assumption.**

Heatwaves that were once rare are becoming normal. Mortality rises, and these shocks now hit several countries at the same time, which erodes the diversification insurers rely on.

The motivation of this work is to build a clear pipeline: **from climate variables, to mortality, and on to insurer liabilities and capital.** The output is a climate-augmented mortality framework and a structured four-country comparison.

- **Scope:** Italy, France, Germany and the Netherlands, under the Solvency II regulatory framework.
- **Approach:** a quantitative pipeline linking climate variables to the latent mortality index, and on to insurer liabilities and capital.
- **Output:** a climate-augmented mortality framework and a structured four-country comparison.

### Research Questions

This thesis asks whether climate change has begun to move the mortality assumptions at the heart of life-insurance valuation — and whether Europe's insurers price that risk consistently.

1. **Empirical:** how much does climate change move mortality, and through which variables?
2. **Comparative:** do the four markets differ in exposure — and in the institutions that manage it? This is what I call the **insurance adaptation gap**.

I test four hypotheses. As the results will show, **all four are supported by the data.**

![Hypotheses](figures/hypotheses.jpg)

---

## Data — What I Used and Why

Everything is built from public, reproducible sources. I collect public data for European countries from **1990 to 2024**:

1. **Mortality** — Human Mortality Database (HMD), the gold standard.
2. **Climate** — ERA5 reanalysis: temperature anomaly and heatwave-day counts.
3. **Pollution** — European Environment Agency: PM2.5, the co-driver.
4. **Socio-economic controls** — Eurostat and the World Bank, to separate the climate signal from income and healthcare trends.

> PM2.5: Particulate matter smaller than 2.5 micrometers.

![Methodology overview](figures/methodology.jpg)

---

## Methodology

### 1. Stochastic Mortality Modeling (Baseline)

**Lee–Carter Model** — the primary decomposition used to model the average age profile of mortality and the mortality time index:

$$\log m_{x,t} = \alpha_x + \beta_x \kappa_t + \epsilon_{x,t}$$

**Cairns–Blake–Dowd (CBD) Model** — an alternative two-factor specification suited for estimating higher-age mortality:

$$\text{logit } q_{x,t} = \kappa_t^{(1)} + (x - \bar{x})\kappa_t^{(2)}$$

**Plat Extension** — an extension of the CBD model that includes a third term to capture year-of-birth cohort effects:

$$\text{logit } q_{x,t} = \kappa_t^{(1)} + (x - \bar{x})\kappa_t^{(2)} + \gamma_{t-x}$$

**Random Walk with Drift** — the classical assumption for projecting the unmodeled stochastic forcing of the mortality time index:

$$\kappa_t = \kappa_{t-1} + \mu + \epsilon_t, \qquad \epsilon_t \sim \mathcal{N}(0, \sigma_\kappa^2)$$

### 2. The Two-Step Climate-Augmented Framework

#### Step 1: Baseline Mortality Estimation

**Country-specific Lee–Carter** — fitted to extract the latent mortality index for each country:

$$\log m_{x,t}^{(c)} = \alpha_x^{(c)} + \beta_x^{(c)} \kappa_t^{(c)} + \epsilon_{x,t}^{(c)}$$

**Singular-Value Decomposition (SVD)** — rank-1 approximation of the centered log-mortality matrix for the Lee–Carter parameter estimates:

$$\tilde{M}^{(c)} \approx \sigma_1^{(c)} u_1^{(c)} v_1^{(c)\prime}$$

**Poisson Likelihood Formulation** — robustness check to handle low death-count cells more securely:

$$\log L = \sum_{x,t} \left[d_{x,t}^{(c)} \log m_{x,t}^{(c)} - E_{x,t}^{(c)} m_{x,t}^{(c)} - \log d_{x,t}^{(c)}!\right]$$

**Binomial Likelihood for CBD** — maximum-likelihood estimation for the complementary CBD specification:

$$L_{CBD} = \prod_{x,t} \binom{E_{x,t}^{(c)}}{d_{x,t}^{(c)}} \left[q_{x,t}^{(c)}\right]^{d_{x,t}^{(c)}} \left[1 - q_{x,t}^{(c)}\right]^{E_{x,t}^{(c)} - d_{x,t}^{(c)}}$$

**Drift Parameter Estimation** — estimated from the 1990–2024 historical sample to calibrate forecast uncertainty:

$$\hat{\mu}^{(c)} = \frac{1}{T-1} \sum_{t=2}^T \left(\hat{\kappa}_t^{(c)} - \hat{\kappa}_{t-1}^{(c)}\right)$$

#### Step 2: The Climate-Augmented Regression

**Core climate-augmented specification** — regresses the first-differenced mortality index on contemporaneous and lagged climate variables along with socio-economic controls:

$$\Delta\hat{\kappa}_t^{(c)} = \mu^{(c)} + \delta^{(c)} \text{TempAnom}_t + \gamma^{(c)} \text{Heatwave}_t + \theta^{(c)} \text{PM2.5}_t + \zeta^{(c)\prime} X_t^{(c)} + u_t^{(c)}$$

**Pooled four-country panel regression** — estimates an EU-average elasticity by stacking the four country-specific equations with fixed effects:

$$\Delta\kappa_{c,t} = \mu_c + \delta\, \text{TempAnom}_{c,t} + \gamma\, \text{Heatwave}_{c,t} + \theta\, \text{PM2.5}_{c,t} + \zeta^\prime X_{c,t} + \nu_{c,t}$$

**Lag-augmented specification (harvesting test)** — evaluates cross-year mortality-displacement effects:

$$\Delta\hat{\kappa}_t^{(c)} = \mu^{(c)} + \delta_0^{(c)} \text{TempAnom}_t + \delta_1^{(c)} \text{TempAnom}_{t-1} + \zeta^{(c)\prime} X_t^{(c)} + u_t^{(c)}$$

#### Step 3: Forward-Looking Stress Testing (NGFS Engine)

Instead of predicting a single deterministic future, the project builds a simulation engine aligned with the **Network for Greening the Financial System (NGFS)** parameters up to the year 2050.

| Scenario | Temperature Target | Heatwave Days / Year | PM2.5 Path | Actuarial Target |
|---|---|---|---|---|
| **S1: Net Zero 2050** | +1.5 °C | 18–22 days | Declines to 7 µg/m³ | Benchmark Baseline |
| **S2: Delayed Transition** | +2.0 °C | 28–32 days | Slowly falls to 10 µg/m³ | Medium Longevity Shock |
| **S3: Hot House World** | +3.0 °C | 45–55 days | Stagnates at 14 µg/m³ | Severe Capital Stress |

**Simulation execution:** the module runs **10,000 bootstrap iterations** on the residual variance ($\hat{\sigma}^2_\kappa$) to output dynamic mortality trajectories ($\hat{\kappa}_{t,s}$) presented as probabilistic fan charts for management review.

#### Step 4: Asset Liability Management (ALM) & Solvency II Integration

This is the financial translation layer. It bridges epidemiology and capital allocation by assessing a representative **€2 billion life portfolio**.

**Best-Estimate Liability (BEL)** — present value of future cash flows under projected mortality trajectories and discount rates:

$$BEL^{(s)} = \sum_{t=1}^T \frac{\mathbb{E}\left[CF_t^{(s)}\right]}{\prod_{u=1}^t (1+r_u)}$$

**SCR Mortality Stress** — standard mortality stress augmented by climate-specific overlays:

$$SCR_{\text{mortality}} = BEL\big|_{q \to q \cdot (1.15) \cdot (1+\delta\Delta T)} - BEL$$

**SCR Longevity Stress** — standard longevity stress offset against climate impacts:

$$SCR_{\text{longevity}} = BEL\big|_{q \to q \cdot (0.80) \cdot (1-\delta\Delta T)} - BEL$$

**SCR Catastrophe** — required reserve additions under peak extreme events:

$$SCR_{\text{cat}} = \text{Cat overlay for heatwave peak scenario}$$

---

## Empirical Results — H1 & H2

This is the central finding. **Climate raises mortality — and the risk is systematic in every country.**

Temperature elasticities are highest in **Italy (δ = 0.051)**, followed by **France (0.043)**, **Germany (0.039)**, and the **Netherlands (0.028)**. The heatwave-day and PM2.5 coefficients follow the same ordering.

The effect is strongest in Italy and weakest in the Netherlands, because Italy has the oldest population and a Mediterranean climate.

Hypothesis two tests whether climate explains year-to-year changes in mortality. **Climate explains between 29% and 47% of these changes**, depending on the country. This shows that climate is not just random noise — it is an important driver of mortality.

![H1 and H2 results](figures/h1_h2_results.jpg)

---

## Empirical Results — H3 & H4

Do climate-augmented models actually forecast better? **The answer is yes.**

- On a 2020–2024 out-of-sample holdout, the augmented model **reduces forecasting error by roughly 10 to 15 percent** relative to the Lee–Carter baseline.
- Climate captures up to **47 percent** of the mortality index's variation, and **AIC and BIC favour the climate model in every country** — confirming H3 and H4.
- The result is solid even dropping the extreme years 2003, 2017 and 2022 together: δ falls about 30 percent but stays significant.

**The effect is real, not driven by a few hot summers.**

![H3 and H4 results](figures/h3_h4_results.jpg)

---

## Case Study: Solvency II

### Translating elasticities into capital on a €2 bn portfolio

I translate the mortality results into financial impacts for a **€2 billion life insurance portfolio** across the four countries. Under the **+3 °C** climate scenario:

- Total insurance liabilities increase by about **6%**.
- By country, the liability impact tracks the elasticity exactly: **Italy +9.3%**, down to the **Netherlands +4.9%**.
- The impact differs by product: **term life insurance is affected the most**, while annuities are less affected because higher mortality reduces future annuity payments.
- The **capital requirement increases by 46%**, mainly due to catastrophe risk.
- All insurers remain above the regulatory minimum, but **solvency falls from 185% to 121%**, with Italy the most affected at **118%**.
- Overall, **Italy experiences the largest financial impact**, while the **Netherlands is the least affected**.

![Case study — Solvency II](figures/case_study_solvency.jpg)

### The insurance adaptation gap — exposure vs. integration

I place each country on two axes: **climate exposure** and **institutional integration**. The diagonal from the Netherlands to Italy defines an *insurance adaptation gap*, split into exposure (climate + demography) and adaptation (institutions).

- **France:** High exposure, strong adaptation → lower impact.
- **Netherlands:** Low exposure, strong institutions → lowest risk.
- **Germany:** Adaptation improving after the 2021 flood shock.
- **Italy:** High exposure, less integration → highest risk.

![Four-country comparison — adaptation gap](figures/comparison_adaptation_gap.jpg)

---

## Conclusions

**Climate is a measurable, systematic, capital-relevant feature of the mortality process — and Europe prices it unevenly.**

- **Empirical:** temperature elasticity 0.028–0.051 per °C; climate explains up to 47% of mortality-index variance.
- **Financial:** +3 °C ⇒ BEL +5.9%, SCR +46%, solvency ratio −49 to −67 ppt on a €2 bn book.
- **Comparative:** an adaptation gap — Italy most exposed yet least integrated; the Netherlands the reverse.
- **Policy:** climate-augmented mortality tables; mandatory ORSA climate scenarios; capital relief for verified adaptation; a harmonised EU framework.

> **Climate risk is a structural feature of the liability side — not a peripheral ESG concern.**

![Conclusion](figures/conclusion.jpg)

---

## Repository Structure

```
├── README.md                 # This file
├── figures/                  # Presentation figures
├── thesis/                   # Full thesis PDF (add: thesis_Kamal-el-halfaoui.pdf)
├── code/                     # Replication code (Step 1: R/StMoMo · Steps 2–4: Python)
└── data/                     # Public data sources (HMD, ERA5, EEA, Eurostat/World Bank)
```

## Data Sources

| Domain | Source | Access |
|---|---|---|
| Mortality | Human Mortality Database (HMD) | [mortality.org](https://www.mortality.org) |
| Climate | ERA5 Reanalysis — Copernicus C3S | [cds.climate.copernicus.eu](https://cds.climate.copernicus.eu) |
| Pollution | European Environment Agency (EEA) | [eea.europa.eu](https://www.eea.europa.eu) |
| Controls | Eurostat · World Bank · OECD | public APIs |

## Citation

```bibtex
@mastersthesis{elhalfaoui2026climate,
  author  = {El Halfaoui, Kamal},
  title   = {Climate Risks in Life Insurance: A Comparative Analysis of Mortality
             and Actuarial Modeling among European Countries under Solvency II},
  school  = {Universit{\`a} degli Studi di Firenze, Scuola di Economia e Management},
  year    = {2026},
  type    = {MSc Thesis, Finance and Risk Management}
}
```

## Keywords

`Climate risk` · `Life insurance` · `Mortality modelling` · `Lee–Carter` · `CBD` · `Solvency II` · `NGFS scenarios` · `Comparative analysis` · `ESG`
