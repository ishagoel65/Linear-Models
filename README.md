# Analyzing Determinants of Happiness Scores (2013–2016)

## Overview

A statistical analysis of the World Happiness Report data to identify how economic and social factors influence national happiness scores across countries from 2013 to 2016.

## Objectives

- Examine which economic and social indicators drive national happiness scores
- Quantify the individual and combined effect of predictors through regression modeling
- Select the best-fitting model using principled statistical criteria

## Dataset

### Source

The data comes from the **[Gallup World Poll](https://www.gallup.com/analytics/318875/global-research.aspx)**, compiled and published annually as the **[World Happiness Report](https://worldhappiness.report/)**. Gallup surveys approximately 1,000 respondents per country each year using nationally representative samples.

- **Coverage:** 158 countries, data from 2013–2016
- **Unit of observation:** One row = one country (for a given year)

---

### What the dataset measures

 The **Happiness Score** is the national average of these responses — it is the target variable in this project.

The six factor columns (Economy through Generosity) are **not raw measurements**. The WHR team runs an OLS regression of Happiness Score against each factor and extracts each factor's **estimated contribution** to the score, anchored against a hypothetical country called *Dystopia* — defined as having the world's lowest observed value on every factor. The sum of all six contributions plus the Dystopia residual equals the Happiness Score exactly.

---

### Happiness Score: highest and lowest

| | Country | Region | Happiness Score |
|---|---------|--------|----------------|
| **Highest** | Switzerland | Western Europe | **7.587** |
| **Lowest** | Togo | Sub-Saharan Africa | **2.839** |

The gap of ~4.75 points between the happiest and least happy country in this dataset illustrates the wide cross-national inequality in subjective wellbeing. The global mean is **5.376** (SD = 1.145), with Western Europe and North America clustered at the top and Sub-Saharan Africa concentrated at the bottom.

---

### Column descriptions and ranges

#### Identifier columns

| Column | Type | Values |
|--------|------|--------|
| `Country` | Categorical | 158 countries |
| `Region` | Categorical | Western Europe, North America, Latin America and Caribbean, Middle East and Northern Africa, Australia and New Zealand, Sub-Saharan Africa, Southeastern Asia, Southern Asia, Central and Eastern Europe, Eastern Asia |

#### Target variable

| Column | Min | Max | Mean | SD |
|--------|-----|-----|------|----|
| `Happiness Score` | 2.839 (Togo) | 7.587 (Switzerland) | 5.376 | 1.145 |

#### Diagnostic columns (not used as predictors)

| Column | Range | Notes |
|--------|-------|-------|
| `Happiness Rank` | 1 – 158 | Derived directly from score. Redundant with Happiness Score — excluded from regression. |
| `Standard Error` | 0.019 (Germany) – 0.137 (Jamaica) | Sampling uncertainty in the score. Higher SE = smaller or less representative Gallup sample. Not used as a predictor. |

#### Predictor columns (factor contributions)

All six predictors are in **the same unit: ladder-score points contributed**. Economy contributing 1.39 for Switzerland means GDP alone explains 1.39 out of its 7.587 score. This makes them directly addable — but not directly comparable by magnitude, since each factor has a different natural range across countries (see note below).

| Column | Min | Max | Mean | SD | Min country | Max country | Description |
|--------|-----|-----|------|----|-------------|-------------|-------------|
| `Economy (GDP per Capita)` | 0.000 | 1.690 | 0.846 | 0.403 | Congo (Kinshasa) | Qatar | Contribution of log GDP per capita to the happiness score. Higher GDP = higher contribution. Strongest predictor with widest spread. |
| `Family` | 0.000 | 1.402 | 0.991 | 0.272 | Central African Republic | Iceland | Contribution of social support, based on: *"If in trouble, do you have relatives or friends to count on?"* Highest mean — most universally present factor. |
| `Health (Life Expectancy)` | 0.000 | 1.025 | 0.630 | 0.247 | Sierra Leone | Singapore | Contribution of healthy life expectancy at birth. Reflects both quality of healthcare and standard of living. |
| `Freedom` | 0.000 | 0.670 | 0.429 | 0.151 | Iraq | Norway | Contribution of perceived freedom to make life choices. Based on: *"Are you satisfied with your freedom to choose what to do with your life?"* |
| `Trust (Government Corruption)` | 0.000 | 0.552 | 0.143 | 0.120 | Indonesia | Rwanda | Contribution of perceived absence of corruption in government and business. Higher value = more trust, less perceived corruption. Most countries cluster near 0 — heavily right-skewed. |
| `Generosity` | 0.000 | 0.796 | 0.237 | 0.127 | Greece | Myanmar | Contribution of charitable giving, adjusted for GDP. Based on: *"Have you donated money to a charity in the past month?"* Does not track wealth — Myanmar scores highest despite being lower-income. |

> **Key observations:**
> - **Economy** has the widest spread (SD = 0.403) — the largest differentiator between wealthy and poor nations.
> - **Trust** is the most right-skewed — most countries score below 0.2; only a handful (Rwanda, Qatar, Singapore) score high.
> - **Family** has the highest mean (0.991) — social support is the most universally present factor across countries.
> - **Generosity** is the noisiest predictor — Myanmar ranks highest despite being a lower-income country, showing that charitable giving doesn't track wealth.

---

### Note on comparability across columns

Since the six factor columns have different ranges, their raw values **cannot be directly compared** to judge which factor matters more. For example, Economy reaching 1.69 while Freedom only reaches 0.67 does not mean Economy contributes more — it reflects that GDP varies more steeply across countries than freedom does.

To fairly compare the importance of predictors, two approaches were used:
- **Standardised coefficients** — each predictor was centred and scaled (subtract mean, divide by SD) before fitting, so coefficients reflect importance in units of standard deviations
- **Partial R²** — measures how much each predictor uniquely explains of the variance in Happiness Score, independent of the others

---

### Sample data (top 5 countries)

| Country | Region | Rank | Score | Economy | Family | Health | Freedom | Trust | Generosity |
|---------|--------|------|-------|---------|--------|--------|---------|-------|------------|
| Switzerland | Western Europe | 1 | 7.587 | 1.397 | 1.350 | 0.941 | 0.666 | 0.420 | 0.297 |
| Iceland | Western Europe | 2 | 7.561 | 1.302 | 1.402 | 0.948 | 0.629 | 0.141 | 0.436 |
| Denmark | Western Europe | 3 | 7.527 | 1.325 | 1.361 | 0.875 | 0.649 | 0.484 | 0.341 |
| Norway | Western Europe | 4 | 7.522 | 1.459 | 1.331 | 0.885 | 0.670 | 0.365 | 0.347 |
| Canada | North America | 5 | 7.427 | 1.326 | 1.323 | 0.906 | 0.633 | 0.330 | 0.458 |

## Methodology

1. **Exploratory Data Analysis** – Distribution checks, correlation plots, outlier detection
2. **Simple Linear Regression** – Individual effect of each predictor on happiness score
3. **Multiple Linear Regression** – Combined effect of all predictors
4. **Stepwise Model Selection** – Forward/backward selection to identify the most parsimonious model
5. **Hypothesis Testing** – t-tests and F-tests for coefficient significance
6. **Model Diagnostics** – Residual plots, normality checks (Q-Q plot, Shapiro-Wilk), homoscedasticity, VIF for multicollinearity

## Hypothesis Testing

Since the analysis was exploratory, no directional hypotheses were pre-specified. Instead, formal significance tests were used to determine which predictors meaningfully contribute to explaining Happiness Score.

### 1. F-test — Overall model significance

Tests whether the regression model as a whole explains a significant portion of variance in Happiness Score.

- **H₀:** All regression coefficients are zero — β₁ = β₂ = ... = βₖ = 0 (the model has no explanatory power)
- **H₁:** At least one βᵢ ≠ 0 (the model explains significant variance)
- **Decision rule:** Reject H₀ if p-value < 0.05
- **Interpretation:** A significant F-test confirms the set of predictors jointly explains happiness scores better than a model with no predictors.

### 2. t-tests — Individual coefficient significance

For each predictor xᵢ, tests whether it has a significant linear relationship with Happiness Score after accounting for all other predictors.

- **H₀:** βᵢ = 0 (predictor xᵢ has no effect on Happiness Score)
- **H₁:** βᵢ ≠ 0 (predictor xᵢ has a significant effect)
- **Test statistic:** t = β̂ᵢ / SE(β̂ᵢ), follows a t-distribution under H₀
- **Decision rule:** Reject H₀ if |t| > t-critical, equivalently if p-value < 0.05

This was applied to each of the six predictors: Economy, Family, Health, Freedom, Trust, and Generosity.

### 3. Stepwise model selection

After individual significance testing, stepwise selection (forward and backward) was used to retain only the predictors that remained significant, arriving at the final model with Adjusted R² ≈ 0.768.

> **Note:** Since all six predictor columns are contributions derived from the same Gallup regression, multicollinearity was checked using VIF before interpreting individual t-test results. High VIF would inflate standard errors and make t-tests unreliable.

## Results

- Best model achieved **Adjusted R² ≈ 0.768**
- All classical linear regression assumptions validated (linearity, normality, homoscedasticity, independence)
- Identified key drivers of happiness at the national level

## Key Learnings

- Practical application of OLS regression diagnostics
- Model selection using AIC/BIC and adjusted R²
- Interpreting regression coefficients in a socioeconomic context

## Files
```
├── SLRM X1,X2,X3,X4.xlsx       # Dataset — simple linear regression data for predictors 
├── MLRM ANALYSIS.xlsx           # Dataset — multiple linear regression analysis
├── Report.pdf                   # Full project report with analysis, plots, and findings
└── README.md                    # Project overview (this file)
```


---
