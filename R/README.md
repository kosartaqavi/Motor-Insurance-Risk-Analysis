## Exploratory Data Analysis (EDA)

`aggregate(expected_loss \~ factor, data=data, FUN=mean)`

## Result:
1. **Expected loss by driver age:** U-shaped. Young drivers (18–25) and elderly drivers (80+) have higher expected loss.
2. **Expected loss by vehicle age:** Newer vehicles have higher expected loss than older vehicles.
3. **Expected loss by BonusMalus:** Strong positive relationship.
4. **Expected loss by Area:** Area F > Area E  > Area D > Area C > Area B > Area A.
5. **Expected loss by Region:** Corse > Bretagne > Ile-de-France > ... > Haute-Normandie.
6. **Expected loss by VehBrand:** B12  > B2  > B5 > ... > B14 .
7. **Expected loss by VehGas:** Regular > Diesel.

## How we derived it:
- aggregate(expected_loss \~      factor, data=data, FUN=mean) for each factor.
- It shows mean expected loss for      each factor.
- The aggregates are **unadjusted**      means (not controlling for other variables).
- ** unadjusted** — it does not control for other      variables. For example, the high expected loss in Corse may partly reflect      the age and BonusMalus distribution of its policyholders, not only the      region effect. The **adjusted** effects are given by the GLM      coefficients, which hold other variables constant.

---

## Insurance KPIs

## Result:

| KPI | Formula | Value |
|---|---|---|
| **Claim Frequency** | `sum(ClaimNb) / sum(Exposure)` | 0.0532 claims per policy-year |
| **Average Severity** | `mean(total claim amount / ClaimNb) for ClaimNb > 0` | 2,221.4 |
| **Total Claim Cost** | `sum(total claim amount)` | — |
| **Loss Cost / Pure Premium** | Total Claim Cost / Total Exposure | ≈ 50 per policy-year (mean expected loss) |

## Why we got this result:
- **Claim Frequency** = 0.0532 means about 5.3 claims      per 100 policy-years. This is typical for MTPL insurance.
- **Average Severity** = 2,221.4 is the mean claim      cost among policies with claims. This is right-skewed (median = 1,172,      mean = 2,221).
- **Pure Premium** ≈ 50 is the average expected      loss per policy.

---

## Frequency Modeling (Poisson GLM)
- **Model:** `glm(ClaimNb \~ VehPower + VehAge + DrivAge + BonusMalus + VehBrand + VehGas + Area + Density + Region + offset(log(Exposure)), family = poisson(link = "log"))`
- **Why Log Link in the Poisson      Model?**

The Poisson GLM uses a log link because it ensures the predicted values are always positive (claim counts cannot be negative). The model is:

`log(Expected ClaimNb) = β₀ + β₁X₁ + ... + offset(log(Exposure))`

This means the effects of predictors are multiplicative on the original scale, not additive. Taking exp(coef) gives the rate ratio — the multiplicative change in claim frequency per one-unit increase in the predictor.

Example: BonusMalus = +0.0225 → exp(0.0225) = 1.0227 → each one-unit increase in BonusMalus multiplies the expected claim count by 1.0227, i.e., +2.27%.
- **Significant variables (p <      0.05):**      VehPower, VehAge, DrivAge, BonusMalus, VehBrandB12, VehBrandB14,      VehBrandB5, VehGasRegular, AreaB, AreaC, AreaD, AreaE, AreaF,      RegionAuvergne
- **Not significant:** Density, most Region levels,      most VehBrand levels

## Note on Offset:

An offset is a variable included in a model with a fixed coefficient of 1 that is not estimated. We use offset(log(Exposure)) to model frequency per unit of exposure. This is needed in the frequency model because ClaimNb is proportional to Exposure — a policy active for 2 years has twice the chance of a claim as one active for 1 year. However, we do not use an offset in the severity model because Severity is the cost per claim, which does not depend on how long the policy was active.

## Key coefficients

| Variable | Coefficient | Rate Ratio (exp(coef)) | Interpretation |
|---|---:|---:|---|
| BonusMalus | +0.0225 | 1.0227 | +1 unit → +2.27% claims |
| VehAge | -0.0388 | 0.9620 | +1 year → -3.80% claims |
| DrivAge | +0.0065 | 1.0065 | +1 year → +0.65% claims |
| VehPower | +0.0134 | 1.0135 | +1 HP → +1.35% claims |
| VehGasRegular | +0.0519 | 1.0533 | Regular vs Diesel → +5.33% claims |
| AreaE | +0.2275 | 1.2555 | Area E vs A → +25.55% claims |
| AreaF | +0.2315 | 1.2605 | Area F vs A → +26.05% claims |
| VehBrandB12 | +0.1550 | 1.1676 | Brand B12 vs B1 → +16.76% claims |

## Why we got this result:
- Poisson regression is appropriate because ClaimNb is a count variable. - The log link ensures predictions are positive. - The offset log(Exposure) accounts for different exposure times. - BonusMalus has the strongest effect — it's designed to capture prior claim history.

## How we derived it:
- glm() with family = poisson(link = "log"). - summary(poisson_model) gave coefficients and significance. - exp(coef(poisson_model)) gave rate ratios.

## Overdispersion Check
- **Mean(ClaimNb) = 0.0532**
- **Var(ClaimNb) = 0.0577**
- **Dispersion = deviance /      df.residual = 0.3205**

**Conclusion:** **No overdispersion.** In fact, the model is **underdispersed** (dispersion < 1).

## Why we got this result:
- For a Poisson distribution, mean = variance. Here, 0.0532 ≈ 0.0577, so Poisson is appropriate. - The dispersion parameter 0.3205 < 1 indicates underdispersion, meaning the model is conservative (predictions are not too variable). - Overdispersion would require dispersion > 1, which would suggest Negative Binomial or Quasi-Poisson.

---

## Severity Modeling (Gamma vs. Lognormal)
- **Severity data:** Only records with ClaimNb >      0 AND total claim amount > 0 → 24,944 records.
- **Note:** 9,116 records had ClaimNb >      0 but total claim amount = 0 — excluded as data quality issue.

## Gamma GLM:
- `glm(Severity \~ ..., family = Gamma(link = "log"))`
- Dispersion: 44.78

## Lognormal Model:
- `lm(log(Severity) \~ ...)`
- Significant: VehAge (p = 0.00532), DrivAge (p < 2e-16), BonusMalus (p < 2e-16), VehBrandB12 (p = 4.59e-13), VehGasRegular (p = 0.03599), RegionCorse (p = 0.02492)

## Model comparison (test set):

| Metric | Gamma | Lognormal |
|---|---:|---:|
| MAE | 1,920.89 | **1,304.65** |
| RMSE | **6,814.67** | 6,830.36 |

**Conclusion:** Lognormal has lower MAE (better for typical claims). Gamma has slightly lower RMSE (better for extreme claims). **We chose Lognormal** for the final expected loss calculation.

## Why we got this result:
- Severity is positive and right-skewed → Gamma and Lognormal are both appropriate. - Lognormal assumes log(Severity) is normal, which is a common actuarial assumption. - The lower MAE for Lognormal means it predicts typical claims better.

## How we derived it:
- glm(..., family = Gamma(link = "log")) and lm(log(Severity) \~ ...). - predict(..., type = "response") for Gamma, exp(predict(...)) for Lognormal. - MAE and RMSE computed on 20% test set.

---

## Frequency × Severity = Expected Loss
- **Mean expected loss:** ≈ 50 per policy
* Expected loss combines frequency      and severity into a single risk metric.
* This is the **pure premium**      — the actuarially fair premium before expenses and profit.

## Model Validation

## Frequency model:
- Predicted sum: 7,253.38 vs. Actual sum: 7,113 → **2% overprediction** - MAE: 0.0983 - RMSE: 0.2349 - Mean Poisson deviance: 0.3148

## Severity model (Lognormal):
- MAE: 1,304.65 - RMSE: 6,830.36

## Why we got this result:
- The frequency model is well-calibrated (predicted ≈ actual). - The severity model has high RMSE due to the heavy-tailed nature of claim severity. - MAE is more interpretable for typical cases; RMSE penalizes large errors more.

## How we derived it:
- 80/20 train/test split with set.seed(123). - predict(poisson_train, newdata = test_data, type = "response"). - MAE = mean(abs(actual - predicted)), RMSE = sqrt(mean((actual - predicted)^2)).

---

## Final Report (Risk Factor Analysis)

## Which factors have the highest impact on claims?

| Factor | Frequency Impact | Severity Impact | Overall Impact |
|---|---|---|---|
| **BonusMalus** | Strongest (LRT = 4388.8) | Strong (F \= 211.0) | **Highest** |
| **DrivAge** | Strong (LRT = 265.6) | Strong (F \= 79.6) | High |
| **VehAge** | Strong (LRT = 1156.2) | Moderate (F = 5.7) | High |
| **VehBrand** | Moderate (LRT = 138.7) | Moderate (F = 7.1) | Moderate |
| **Area** | Moderate (LRT = 147.8) | Not significant (F = 0.72) | Frequency-driven |
| **Region** | Moderate (LRT = 172.0) | Weak (F = 1.84) | Moderate |
| **VehGas** | Weak (LRT \= 22.6) | Weak (F = 4.6) | Low |
| **VehPower** | Weak (LRT \= 23.5) | Not significant (F = 0.01) | Low |
| **Density** | Not significant (LRT = 0.2) | Not significant (F = 0.02) | None |

The f value and p value determine if the factor has meaningful effect, while LRT shows how big that effect is, and the coefficient of that factor in model, shows the relationship of factor and response variable.

## Key findings:
1. **BonusMalus** is the strongest predictor of both frequency and severity. Policies with BonusMalus = 230 have expected loss 165× higher than those with BonusMalus = 51.
2. **DrivAge** shows a U-shape: young (18–25) and old (80+) drivers have higher expected loss.
3. **VehAge:** Newer vehicles (VehAge = 1) have higher expected loss than older vehicles (VehAge = 60).
4. **Area F** and **Region Corse** have the highest expected loss.
5. **Density** is not significant — surprising, but may be captured by Area/Region.

## why we got this result:
- drop1(test = "Chisq") for frequency and drop1(test = "F") for severity give likelihood ratio tests and F-tests for each variable's contribution. - The LRT/F values measure how much the model fit degrades when the variable is removed. - BonusMalus has the largest LRT/F, meaning it contributes the most to model fit.

## How we derived it:
- drop1(poisson_model, test = "Chisq") → LRT for frequency. - drop1(lognormal_model, test = "F") → F-test for severity.

## How Important Factors Affect Expected Loss:

To understand how each important factor affects **Expected Loss**, you must look at the coefficient of that factor in **both** the frequency model (summary(poisson_model)) and the severity model (summary(lognormal_model)). Since:

Expected Loss = Expected Frequency × Expected Severity

the direction of the effect on Expected Loss is determined by the **combination** of the signs of the two coefficients:
- If **both coefficients are positive** → the factor **increases** Expected Loss. - If **both coefficients are negative** → the factor **decreases** Expected Loss. - If **one is positive and the other is negative** → the effect is **ambiguous** (partially offsetting). - If the coefficient is **not significant** in one model → only the significant model drives the effect.

For **numerical variables**, the combined effect on Expected Loss can be computed as:

Effect on Expected Loss = exp(β_frequency + β_severity) − 1

where β_frequency is the coefficient from the Poisson model and β_severity is the coefficient from the lognormal model. The result is the **percentage change in Expected Loss per one-unit increase** in the variable.

For **categorical variables**, the same formula applies, but the coefficient represents the effect of that level **relative to the reference level**:

Effect on Expected Loss = exp(β_frequency + β_severity) − 1

where β are the coefficients for that specific level (compared to the reference level).

## Summary of methodology:

Interpreting summary(model)

For categorical variables, R drops one level as the reference (first alphabetical level, e.g., Area A, Diesel, Brand B1). Coefficients for other levels represent the difference relative to the reference on the log scale. A p-value in summary() tests whether that specific level differs from the reference — not from all other levels.

Example: AreaE = +0.2275 (p < 2e-16) means Area E has significantly more claims than Area A (reference). exp(0.2275) = 1.2555 → +25.55% claims vs Area A.

For numerical variables, the coefficient is the change per one-unit increase: BonusMalus = +0.0225 → exp(0.0225) = 1.0227 → +2.27% claims per unit.

## summary() vs. drop1()

| Question | summary() | drop1() |
|---|:---:|:---:|
| Does Area E differ from Area A? | ✅ | ❌ |
| Is Area important overall? | ❌ | ✅ |
| Which variable is most important? | ❌ | ✅ |
| Direction of effect | ✅ | ❌ |
- summary() → tests each level against the reference; gives direction and magnitude. - drop1() → tests the entire variable against the full model; ranks variables by importance (largest LRT/F = most important).

## Both are needed: drop1() says *which variables matter*, summary() says *how they matter*.

Why Chi-square vs. F-test?
- Poisson GLM → fitted by maximum likelihood → drop1(test = "Chisq") (likelihood ratio test, ΔDeviance \~ χ²). - Lognormal (LM) → fitted by OLS → drop1(test = "F") (F-test, ΔRSS ratio \~ F-distribution).

## Use "Chisq" for GLMs (Poisson, Gamma, Binomial). Use "F" for linear models (lognormal, normal).
