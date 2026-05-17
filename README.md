# Airbnb LA Real Estate Revenue Analysis

Revenue modeling for Airbnb entire-home listings across five Los Angeles neighborhoods,
identifying which bedroom–bathroom configurations outperform local and citywide averages.
Built with decision tree regression (+ SHAP analysis) and OLS with interaction terms.

**Team:** Anna Calkins, Clark Enge, Eline Varhaugvik, Maya Jha, Martyna Przeradzka, Sofia Ferreira Hidalgo de Morillo

---

## Scope

The analysis focuses on five neighborhoods selected for their proximity to major event venues,
which makes them structurally comparable and exposes them to similar demand patterns:

| Neighborhood | Venue |
|---|---|
| Hollywood Hills | Hollywood Bowl |
| Inglewood | Kia Forum |
| Downtown LA | Crypto.com Arena |
| Exposition Park | LA Memorial Coliseum |
| Pasadena | Rose Bowl Stadium |

Only entire-home/entire-apartment listings are included, ensuring comparability among
properties intended for full private occupancy.

---

## Data

Source: [Inside Airbnb](http://insideairbnb.com/get-the-data/) — Los Angeles

- `listings.csv.gz` (~600K rows): property attributes, location, bedroom/bathroom counts,
  nightly price, and Inside Airbnb's pre-computed `estimated_revenue_l365d` and
  `estimated_occupancy_l365d` columns
- Revenue target: `estimated_revenue_l365d` — annualized revenue estimate derived from
  observed availability and pricing; used directly without a separate calendar join

After filtering to entire-home listings and removing rows with missing or zero values
for bedrooms, bathrooms, or revenue, the dataset was subset to the five neighborhoods above.

---

## Methods

**Data cleaning** — price parsed from string to float, NA/zero-value rows dropped for
the three key columns (bedrooms, bathrooms, revenue), neighborhood subsets created
and concatenated, neighborhoods one-hot encoded for modeling.

**EDA** — revenue distribution, per-neighborhood boxplots and bar charts with error
bars, price-vs-revenue scatter, and per-neighborhood KDE price distributions.

**Decision Tree Regressor** — trained on bedrooms, bathrooms, and one-hot neighborhood
dummies across all five areas. Evaluated on an 80/20 train-test split and 5-fold
cross-validation (R²). All 180 bedroom × bathroom × neighborhood combinations
projected to generate a lookup table of predicted annual revenues.

**SHAP analysis** — TreeExplainer applied to the global model and then to five separate
per-neighborhood models (bedrooms and bathrooms only), revealing how feature importance
differs by area.

**OLS regression** — baseline model plus an interaction model
(`revenue ~ bedrooms * neighborhood + bathrooms * neighborhood`) with HC3 robust
standard errors to account for heteroscedasticity. Bedroom and bathroom interaction
terms jointly tested for significance via F-tests.

---

## Key Findings

**Revenue distribution**
The distribution is right-skewed: most listings earn $5K–$50K annually, a mid-tier
earns $50K–$100K, and a small premium segment exceeds $150K. Hollywood Hills produces
the most high-revenue outliers with the widest spread; Exposition Park reaches the same
median with far less variability.

**OLS interaction model**
- Bedroom × neighborhood interactions are jointly significant (F-test p ≈ 0.00),
  confirming that the bedroom–revenue relationship is not uniform across LA
- Hollywood Hills is the only neighborhood where the bedroom interaction term is
  individually significant (p = 0.014)
- Bathroom × neighborhood interactions are not jointly significant (p = 0.072),
  suggesting bathrooms alone don't reliably drive revenue across neighborhoods

**SHAP feature importance (global model)**
Bedrooms are the most important feature overall, followed by bathrooms, then the
Hollywood Hills and Pasadena neighborhood dummies. Inglewood and Exposition Park
dummies have minimal impact on the global model.

**Per-neighborhood SHAP patterns**

| Neighborhood | Bedrooms | Bathrooms |
|---|---|---|
| Hollywood Hills | More → higher revenue | Strong positive effect; larger upside than bedrooms |
| Pasadena | Non-linear; high or low can increase or decrease revenue | Most influential feature; both large positive and negative impacts |
| Inglewood | More → exponentially higher revenue | Weak overall effect |
| Downtown | Fewer → higher revenue | Fewer → higher revenue (compact, high-demand units) |
| Exposition Park | Fewer → higher revenue (similar to Downtown) | Neutral |

**Top configurations vs. benchmarks**

| Neighborhood | Best config | vs. global avg | vs. neighborhood avg |
|---|---|---|---|
| Exposition Park | 3–5 bed / 1 bath | +86.9% | +115.4% |
| Hollywood Hills | 4 bed / 3.5 bath | +143.4% | +62.5% |
| Inglewood | 4 bed / 2 bath | +186.7% | +281.8% |
| Pasadena | 5 bed / 3–3.5 bath | +284.7% | +222.6% |
| Downtown LA | 3–5 bed / 2.5 bath (~$81K projected) | +419.4% | +745.0% |

Downtown's top configuration produces the largest outperformance relative to both
benchmarks, making it the strongest candidate for investment given these projections.

**Configurations to avoid**
- Exposition Park: 0–2 bed / 2.5–3.5 bath
- Inglewood: 2 bed / 1.5 bath; 0–2 bed / 2.5–3.5 bath
- Pasadena: 3–5 bed / 1.5 bath
- Downtown: 3–5 bed / 3 bath (one additional half-bath from the peak drops sharply)

---

## File Structure

```
AirbnbLARealEstateRevenueAnalysis/
│
├── 01_cleaning.ipynb       # Data loading, filtering, one-hot encoding, neighborhood splits
├── 02_eda.ipynb            # Revenue distributions, neighborhood boxplots, price scatter
├── 03_modeling.ipynb       # Decision tree, SHAP analysis, OLS + interaction terms, business insights
│
├── data/
│   └── README.md           # Download instructions for listings.csv.gz from Inside Airbnb
│                           # (raw data not committed — ~600K rows)
│
└── README.md
```

> The original `MainFile.ipynb` contains all sections sequentially. The split above
> separates the three natural stages for easier navigation.

---

## Setup

```bash
git clone https://github.com/clarktenge/AirbnbLARealEstateRevenueAnalysis.git
cd AirbnbLARealEstateRevenueAnalysis
pip install pandas numpy matplotlib seaborn statsmodels scikit-learn shap
```

Download `listings.csv.gz` for Los Angeles from [Inside Airbnb](http://insideairbnb.com/get-the-data/)
and place it in `data/`. Update the file path in `01_cleaning.ipynb` to match your local setup.

Run notebooks in order: `01_cleaning` → `02_eda` → `03_modeling`.

---

## Stack

Python · Pandas · NumPy · Matplotlib · Seaborn · Statsmodels · scikit-learn · SHAP · Jupyter / Google Colab
