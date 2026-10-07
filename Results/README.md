# Insurance Risk Analysis — Results

This README presents the **results, key findings, risk-factor analysis, expected loss results, and business recommendations** from the insurance risk modeling project.

---

# 1. Exploratory Data Analysis (EDA)

## Expected Loss by Driver Age

The relationship between driver age and expected loss is **U-shaped**.

* Young drivers (18–25) have higher expected loss.
* Elderly drivers (80+) have higher expected loss.
* Middle-aged drivers have relatively lower expected loss.

---

## Expected Loss by Vehicle Age

Newer vehicles have higher expected loss than older vehicles.

---

## Expected Loss by BonusMalus

There is a **strong positive relationship** between BonusMalus and expected loss.

Higher BonusMalus values are associated with higher expected loss.

---

## Expected Loss by Area

The unadjusted mean expected loss follows:

```text
Area F > Area E > Area D > Area C > Area B > Area A
```

---

## Expected Loss by Region

The unadjusted ranking starts with:

```text
Corse > Bretagne > Ile-de-France > ... > Haute-Normandie
```

---

## Expected Loss by Vehicle Brand

The ranking starts with:

```text
B12 > B2 > B5 > ... > B14
```

---

## Expected Loss by Vehicle Gas Type

```text
Regular > Diesel
```

---

# 2. Important Note About EDA

The EDA results are **unadjusted means**.

They do not control for other variables.

For example, the high expected loss observed in Corse may partly reflect the age and BonusMalus distribution of its policyholders rather than the independent effect of Region.

Therefore, EDA results should not be interpreted as causal or adjusted effects.

The **adjusted effects** are obtained from the GLM coefficients.

---

# 3. Insurance KPIs

| KPI                          | Formula                                              |                             Value |
| ---------------------------- | ---------------------------------------------------- | --------------------------------: |
| **Claim Frequency**          | `sum(ClaimNb) / sum(Exposure)`                       | **0.0532 claims per policy-year** |
| **Average Severity**         | `mean(total claim amount / ClaimNb for ClaimNb > 0)` |                       **2,221.4** |
| **Total Claim Cost**         | `sum(total claim amount)`                            |                             **—** |
| **Loss Cost / Pure Premium** | `Total Claim Cost / Total Exposure`                  |          **≈ 50 per policy-year** |

---

## Claim Frequency

The claim frequency is:

```text
0.0532 claims per policy-year
```

This means approximately:

```text
5.3 claims per 100 policy-years
```

This is typical for MTPL insurance.

---

## Average Severity

The average claim severity is:

```text
2,221.4
```

This is the mean claim cost among policies with claims.

The distribution is right-skewed:

```text
Median = 1,172
Mean = 2,221
```

The difference between the median and mean indicates the presence of high-cost claims.

---

## Pure Premium

The pure premium is approximately:

```text
50 per policy-year
```

This represents the average expected loss per policy.

It is the actuarially fair premium before expenses and profit.

---

# 4. Frequency Modeling Results

A Poisson GLM was used to model claim frequency.

## Significant Variables

The statistically significant variables were:

* VehPower
* VehAge
* DrivAge
* BonusMalus
* VehBrandB12
* VehBrandB14
* VehBrandB5
* VehGasRegular
* AreaB
* AreaC
* AreaD
* AreaE
* AreaF
* RegionAuvergne

---

## Not Significant

The following were not statistically significant:

* Density
* Most Region levels
* Most VehBrand levels

---

# 5. Key Frequency Coefficients

| Variable          | Coefficient | Rate Ratio `exp(coef)` | Interpretation                        |
| ----------------- | ----------: | ---------------------: | ------------------------------------- |
| **BonusMalus**    |     +0.0225 |                 1.0227 | +1 unit → **+2.27% claims**           |
| **VehAge**        |     -0.0388 |                 0.9620 | +1 year → **-3.80% claims**           |
| **DrivAge**       |     +0.0065 |                 1.0065 | +1 year → **+0.65% claims**           |
| **VehPower**      |     +0.0134 |                 1.0135 | +1 HP → **+1.35% claims**             |
| **VehGasRegular** |     +0.0519 |                 1.0533 | Regular vs Diesel → **+5.33% claims** |
| **AreaE**         |     +0.2275 |                 1.2555 | Area E vs A → **+25.55% claims**      |
| **AreaF**         |     +0.2315 |                 1.2605 | Area F vs A → **+26.05% claims**      |
| **VehBrandB12**   |     +0.1550 |                 1.1676 | B12 vs B1 → **+16.76% claims**        |

---

# 6. Interpretation of Frequency Results

Poisson regression is appropriate because `ClaimNb` is a **count variable**.

The log link ensures positive predictions.

The exposure offset accounts for different exposure times between policies.

BonusMalus has a particularly strong relationship with claim frequency, which is consistent with its purpose of capturing previous claim history.

---

# 7. Overdispersion Results

The following statistics were obtained:

| Statistic     |      Value |
| ------------- | ---------: |
| Mean(ClaimNb) | **0.0532** |
| Var(ClaimNb)  | **0.0577** |
| Dispersion    | **0.3205** |

The mean and variance are relatively close:

```text
Mean ≈ Variance
```

The dispersion statistic is:

```text
0.3205
```

which is below 1.

## Conclusion

There is **no evidence of overdispersion**.

The model is, in fact, underdispersed.

Therefore, there is no indication that a Negative Binomial or Quasi-Poisson model is required based on this dispersion check.

---

# 8. Severity Modeling Results

Severity was modeled only for observations satisfying:

```text
ClaimNb > 0
```

and:

```text
total claim amount > 0
```

This produced:

```text
24,944 records
```

There were also:

```text
9,116 records
```

with:

```text
ClaimNb > 0
```

but:

```text
total claim amount = 0
```

These observations were excluded as a data-quality issue.

---

# 9. Gamma vs. Lognormal

| Metric   |        Gamma |    Lognormal |
| -------- | -----------: | -----------: |
| **MAE**  |     1,920.89 | **1,304.65** |
| **RMSE** | **6,814.67** |     6,830.36 |

## Interpretation

The Lognormal model has the lower MAE.

This means it performs better for **typical claims**.

The Gamma model has a slightly lower RMSE.

This means it performs slightly better when large errors and extreme claims receive greater weight.

Because the Lognormal model had substantially lower MAE and is appropriate for positive, right-skewed severity data, the **Lognormal model was selected for the final expected loss calculation**.

---

# 10. Significant Severity Variables

The significant variables in the Lognormal model were:

* VehAge (`p = 0.00532`)
* DrivAge (`p < 2e-16`)
* BonusMalus (`p < 2e-16`)
* VehBrandB12 (`p = 4.59e-13`)
* VehGasRegular (`p = 0.03599`)
* RegionCorse (`p = 0.02492`)

---

# 11. Expected Loss

Expected loss is calculated as:

```text
Expected Loss = Expected Frequency × Expected Severity
```

The mean expected loss is approximately:

```text
50 per policy
```

Expected loss combines claim frequency and claim severity into a single risk measure.

This is the **pure premium**, representing the actuarially fair expected claim cost before expenses and profit.

---

# 12. Model Validation

## Frequency Model

| Metric                |                  Result |
| --------------------- | ----------------------: |
| Predicted sum         |            **7,253.38** |
| Actual sum            |               **7,113** |
| Difference            | **≈ 2% overprediction** |
| MAE                   |              **0.0983** |
| RMSE                  |              **0.2349** |
| Mean Poisson deviance |              **0.3148** |

The predicted number of claims is close to the actual number of claims.

Therefore, the frequency model is reasonably well calibrated.

---

## Severity Model — Lognormal

| Metric |       Result |
| ------ | -----------: |
| MAE    | **1,304.65** |
| RMSE   | **6,830.36** |

The high RMSE is mainly related to the heavy-tailed nature of claim severity.

MAE is more interpretable for typical claims, while RMSE gives greater weight to large prediction errors.

---

# 13. Risk Factor Analysis

The following table summarizes the importance of the main risk factors.

| Factor         | Frequency Impact            | Severity Impact            | Overall Impact       |
| -------------- | --------------------------- | -------------------------- | -------------------- |
| **BonusMalus** | Strongest (LRT = 4388.8)    | Strong (F = 211.0)         | **Highest**          |
| **DrivAge**    | Strong (LRT = 265.6)        | Strong (F = 79.6)          | **High**             |
| **VehAge**     | Strong (LRT = 1156.2)       | Moderate (F = 5.7)         | **High**             |
| **VehBrand**   | Moderate (LRT = 138.7)      | Moderate (F = 7.1)         | **Moderate**         |
| **Area**       | Moderate (LRT = 147.8)      | Not significant (F = 0.72) | **Frequency-driven** |
| **Region**     | Moderate (LRT = 172.0)      | Weak (F = 1.84)            | **Moderate**         |
| **VehGas**     | Weak (LRT = 22.6)           | Weak (F = 4.6)             | **Low**              |
| **VehPower**   | Weak (LRT = 23.5)           | Not significant (F = 0.01) | **Low**              |
| **Density**    | Not significant (LRT = 0.2) | Not significant (F = 0.02) | **None**             |

---

# 14. How to Interpret LRT, F, and Coefficients

The **F-value and p-value** determine whether a factor has a statistically meaningful effect.

The **LRT** shows how much the model fit deteriorates when a factor is removed.

The **coefficient** shows the direction and magnitude of the relationship between a predictor and the response variable.

Therefore:

* **LRT/F → importance of the whole variable**
* **p-value → statistical significance**
* **Coefficient → direction and magnitude of a specific effect**

---

# 15. Key Findings

## 1. BonusMalus

BonusMalus is the strongest predictor of both frequency and severity.

Its importance is reflected by:

```text
Frequency LRT = 4388.8
Severity F = 211.0
```

Policies with:

```text
BonusMalus = 230
```

have an expected loss of approximately:

```text
5,810
```

compared with approximately:

```text
50
```

for a typical policy.

The reported comparison is:

```text
165× higher expected loss than BonusMalus = 51
```

---

## 2. DrivAge

Driver age shows a **U-shaped relationship**.

Young drivers:

```text
18–25
```

and elderly drivers:

```text
80+
```

have higher expected loss.

---

## 3. VehAge

Newer vehicles have higher expected loss.

For example:

```text
VehAge = 1
```

has higher expected loss than:

```text
VehAge = 60
```

---

## 4. Area

Area F has the highest expected loss among the Area categories.

Area E also has high expected loss.

Relative to Area A:

```text
Area E → +25.55%
Area F → +26.05%
```

These effects are primarily **frequency-driven**.

---

## 5. Region

Corse has the highest expected loss among the regions.

The effect is primarily **severity-driven**.

---

## 6. Density

Density is not statistically significant.

This is somewhat surprising because population density might intuitively be expected to influence claim risk.

One possible explanation is that some of the information captured by Density is already represented by Area and Region.

---

# 16. Combined Effect on Expected Loss

Because:

```text
Expected Loss = Frequency × Severity
```

the coefficient from both models must be considered.

For numerical variables:

```text
Effect on Expected Loss =
exp(β_frequency + β_severity) − 1
```

For categorical variables, the same formula is used for the specific category relative to its reference category.

---

# 17. Results Table

| Factor            | Frequency Coefficient (Poisson) | Severity Coefficient (Lognormal) | Direction on Expected Loss       |                 Combined Effect |
| ----------------- | ------------------------------: | -------------------------------: | -------------------------------- | ------------------------------: |
| **BonusMalus**    |            +0.02245 (p < 2e-16) |             +0.00662 (p < 2e-16) | **Increases** (both positive)    |             **+2.95% per unit** |
| **DrivAge**       |            +0.00649 (p < 2e-16) |             +0.00547 (p < 2e-16) | **Increases** (both positive)    |             **+1.20% per year** |
| **VehAge**        |            −0.03878 (p < 2e-16) |             −0.00396 (p = 0.017) | **Decreases** (both negative)    |             **−4.18% per year** |
| **VehPower**      |         +0.01343 (p = 1.12e-06) |   +0.00050 (p = 0.905, not sig.) | **Increases** (frequency-driven) |             **+1.35% per unit** |
| **VehGasRegular** |         +0.05190 (p = 2.06e-06) |             −0.03450 (p = 0.033) | **Ambiguous** (opposite signs)   |          **≈ +1.76% vs Diesel** |
| **Area E**        |            +0.22750 (p < 2e-16) |   −0.04442 (p = 0.244, not sig.) | **Increases** (frequency-driven) |           **+25.55% vs Area A** |
| **Area F**        |          +0.23150 (p = 0.00932) |   −0.18626 (p = 0.166, not sig.) | **Increases** (frequency-driven) |           **+26.05% vs Area A** |
| **Region Corse**  |  +0.12790 (p = 0.236, not sig.) |             +0.39568 (p = 0.030) | **Increases** (severity-driven)  | **+39.57% vs reference region** |
| **VehBrand B12**  |            +0.15500 (p < 2e-16) |          +0.18612 (p = 4.11e-11) | **Increases** (both positive)    |         **+41.47% vs Brand B1** |
| **Density**       | −1.57e-06 (p = 0.671, not sig.) |  +8.55e-07 (p = 0.877, not sig.) | **No effect**                    |                               — |

---

# 18. Interpretation of the Results Table

## BonusMalus

Frequency coefficient:

```text
+0.0225
```

Severity coefficient:

```text
+0.0066
```

Both are positive.

Therefore, BonusMalus increases expected loss.

The combined effect is:

```text
exp(0.0225 + 0.0066) − 1
≈ +2.95%
```

per one-unit increase.

---

## DrivAge

Both frequency and severity coefficients are positive.

Therefore, increasing driver age is associated with increasing expected loss according to the fitted linear specification.

However, the overall EDA relationship is U-shaped, so the linear coefficient should not be interpreted as saying that every older driver is riskier. The age pattern is better understood from the combined model predictions.

---

## VehAge

Both coefficients are negative:

```text
Frequency = −0.03878
Severity = −0.00396
```

Therefore, increasing vehicle age decreases expected loss.

The combined effect is approximately:

```text
−4.18% per year
```

---

## VehPower

The frequency coefficient is positive and statistically significant.

The severity coefficient is not statistically significant.

Therefore, the effect is primarily **frequency-driven**.

---

## VehGasRegular

The frequency coefficient is positive:

```text
+0.05190
```

while the severity coefficient is negative:

```text
−0.03450
```

Therefore, the two effects partially offset each other.

The resulting combined effect is approximately:

```text
+1.76% vs Diesel
```

---

## Area E

Area E has a significant positive frequency effect.

Its severity coefficient is not statistically significant.

Therefore, the higher expected loss is primarily driven by claim frequency.

The effect is:

```text
+25.55% vs Area A
```

---

## Area F

Area F also has a positive frequency effect.

Its severity coefficient is not statistically significant.

Therefore, the higher expected loss is primarily frequency-driven.

The effect is:

```text
+26.05% vs Area A
```

---

## Region Corse

The frequency coefficient is not statistically significant.

However, the severity coefficient is positive and statistically significant:

```text
+0.39568
```

Therefore, the higher expected loss in Corse is primarily **severity-driven**.

The reported effect is:

```text
+39.57% vs reference region
```

---

## VehBrand B12

Both frequency and severity coefficients are positive and statistically significant.

Therefore, B12 has a higher expected loss than the reference brand B1.

The combined effect is:

```text
+41.47% vs Brand B1
```

---

## Density

Neither the frequency nor severity coefficient is statistically significant.

Therefore, there is no statistically significant evidence of an independent Density effect in the fitted models.

---

# 19. Highest-Risk Levels

The highest-risk observations and categories identified by the analysis include:

### BonusMalus

```text
BonusMalus = 230
```

Expected loss:

```text
5,810
```

This is approximately:

```text
165× higher than BonusMalus = 51
```

---

### Area F

```text
+26.05% Expected Loss vs Area A
```

The effect is primarily frequency-driven.

---

### Area E

```text
+25.55% Expected Loss vs Area A
```

The effect is primarily frequency-driven.

---

### Region Corse

The reported result is:

```text
+48.54% Expected Loss vs reference
```

The effect is primarily severity-driven.

---

### Driver Age

The highest-risk age groups are:

```text
18–25
```

and:

```text
80+
```

This reflects the U-shaped relationship between driver age and expected loss.

---

### Vehicle Age

The newest vehicles:

```text
VehAge = 1
```

have higher expected loss than older vehicles such as:

```text
VehAge = 60
```

---

### Vehicle Brand B12

```text
+41.47% Expected Loss vs Brand B1
```

---

# 20. Business Recommendations

## 1. Risk-Based Pricing

Premiums can be set in proportion to expected loss.

For example, policies such as:

```text
IDpol 141668
```

with expected loss:

```text
5,810
```

represent substantially higher expected claim costs.

Low-risk policies with expected loss around:

```text
50
```

can receive relatively lower premiums.

---

## 2. Risk Segmentation

Policies can be classified into:

* **Low Risk**
* **Medium Risk**
* **High Risk**

Standard pricing can be applied to Low- and Medium-risk policies, while High-risk policies can receive stricter underwriting.

---

## 3. BonusMalus Review

Because BonusMalus is the strongest predictor of both frequency and severity, the insurer should review the BonusMalus system to ensure that it accurately reflects risk.

Policies with:

```text
BonusMalus > 150
```

could be flagged for additional review.

---

## 4. Geographic Strategy

### Area E and F

Because these areas have higher claim frequency, insurers could consider:

* Premium adjustments
* Safe-driving incentives
* Targeted risk management

### Region Corse

Because Corse is associated primarily with higher claim severity, insurers could consider:

* Higher deductibles
* Repair-cost controls
* Claims management strategies

---

## 5. Deductible Design

For high-risk groups such as:

* Elderly drivers 80+
* BonusMalus > 150
* Area F

the insurer could offer policies with higher deductibles to reduce severity exposure.

---

## 6. Portfolio Management

The insurer can balance the portfolio by mixing Low- and High-risk policies.

Avoiding excessive concentration in high-risk segments such as:

* Area F
* Corse
* High BonusMalus

can help manage portfolio risk.

---

## 7. Monitoring and Early Warning

Policies with:

```text
predicted_claims > 1.5
```

or:

```text
expected_loss > 3,000
```

could be flagged for manual review before renewal.

---

## 8. Underwriting Decisions

For very high-risk policies with:

```text
Expected Loss > 5,000
```

possible actions include:

* Accept with a higher premium
* Accept with a higher deductible
* Decline coverage

---

## 9. Frequency vs. Severity Strategy

Different risk factors require different management strategies.

### Area E / Area F

These areas primarily affect:

```text
Frequency
```

Therefore, insurers could focus on:

* Safe-driving incentives
* Frequency reduction
* Claims prevention

### Region Corse

Corse primarily affects:

```text
Severity
```

Therefore, insurers could focus on:

* Higher deductibles
* Repair-cost controls
* Claims management

---

## 10. Management Reporting

The key factors that should be highlighted in management reports are:

1. **BonusMalus**
2. **DrivAge**
3. **VehAge**
4. **Area**
5. **Region**

The analysis suggests that these variables provide the most important information for understanding claim risk.

Recommended actions include:

* Risk-based pricing
* Risk segmentation
* BonusMalus review
* Geographic pricing adjustments
* Portfolio rebalancing
* High-risk policy monitoring

