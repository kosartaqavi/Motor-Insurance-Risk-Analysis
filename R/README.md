## Results Table

| Factor | Frequency Coefficient (Poisson) | Severity Coefficient (Lognormal) | Direction on Expected Loss | Combined Effect |
|---|---|---|---|---|
| **BonusMalus** | +0.02245 (p < 2e-16) | +0.00662 (p < 2e-16) | **Increases** (both positive) | +2.95% per unit |
| **DrivAge** | +0.00649 (p < 2e-16) | +0.00547 (p < 2e-16) | **Increases** (both positive) | +1.20% per year |
| **VehAge** | −0.03878 (p < 2e-16) | −0.00396 (p = 0.017) | **Decreases** (both negative) | −4.18% per year |
| **VehPower** | +0.01343 (p = 1.12e-06) | +0.00050 (p = 0.905, not sig.) | **Increases** (frequency-driven) | +1.35% per unit |
| **VehGasRegular** | +0.05190 (p = 2.06e-06) | −0.03450 (p = 0.033) | **Ambiguous** (opposite signs) | ≈ +1.76% vs Diesel |
| **Area E** | +0.22750 (p < 2e-16) | −0.04442 (p = 0.244, not sig.) | **Increases** (frequency-driven) | +25.55% vs Area A |
| **Area F** | +0.23150 (p = 0.00932) | −0.18626 (p = 0.166, not sig.) | **Increases** (frequency-driven) | +26.05% vs Area A |
| **Region Corse** | +0.12790 (p = 0.236, not sig.) | +0.39568 (p = 0.030) | **Increases** (severity-driven) | +39.57% vs reference region |
| **VehBrand B12** | +0.15500 (p < 2e-16) | +0.18612 (p = 4.11e-11) | **Increases** (both positive) | +41.47% vs Brand B1 |
| **Density** | −1.57e-06 (p = 0.671, not sig.) | +8.55e-07 (p = 0.877, not sig.) | **No effect** (neither significant) | — |

## Effect on Expected Loss:

Since Expected Loss = Frequency × Severity, look at the coefficient in both models:

*Effect on Expected Loss = exp(β_frequency + β_severity) − 1*
- Both positive → increases Expected Loss. - Both negative → decreases Expected Loss. - Opposite signs → ambiguous (partially offsetting). - One not significant → only the significant model drives the effect.

Example: BonusMalus (freq +0.0225, sev +0.0066) → exp(0.0291) − 1 = +2.95% per unit.

## Highest-Risk Levels
- **BonusMalus = 230** → Expected Loss = 5,810 (165×      higher than BonusMalus = 51)
- **Area F** → +26.05% Expected Loss vs Area      A (frequency-driven)
- **Area E** → +25.55% Expected Loss vs Area      A (frequency-driven)
- **Region Corse** → +48.54% Expected Loss vs      reference (severity-driven)
- **DrivAge 18–25 and 80+** → U-shaped, both ends have      higher Expected Loss
- **VehAge = 1 (newest vehicles)** → higher Expected Loss than      older vehicles
- **VehBrand B12** → +41.47% Expected Loss vs      Brand B1

---

## Business Recommendations

## 1. Risk-Based Pricing

Set premiums proportional to Expected Loss. Policies like IDpol 141668 (Expected Loss = 5,810) should pay significantly higher premiums. Low-risk policies (Expected Loss ≈ 50) can receive discounts.

## 2. Risk Segmentation

as we Classified policies into Low / Medium / High risk groups, Offer standard pricing to Low and Medium, and apply stricter underwriting to High-risk policies.

## 3. BonusMalus Review

Since BonusMalus is the strongest predictor of both frequency and severity, the insurer should review the BonusMalus system to ensure it accurately reflects risk. Policies with BonusMalus > 150 should be flagged.

## 4. Geographic Strategy
- **Area E and F:** Premiums should be adjusted      upward for higher claim frequency.
- **Region Corse:** Premiums should be adjusted      upward for higher claim severity (higher repair costs).

## 5. Deductible Design

For high-risk groups (elderly drivers 80+, BonusMalus > 150, Area F), offer policies with higher deductibles to reduce claim severity exposure.

## 6. Portfolio Management

Balance the portfolio by mixing Low and High risk policies. Avoid over-concentration in high-risk segments (Area F, Corse, high BonusMalus).

## 7. Monitoring and Early Warning

Flag policies with predicted_claims > 1.5 or expected_loss > 3,000 for manual review before renewal.

## 8. Underwriting Decisions

For very high-risk policies (Expected Loss > 5,000), consider:
- Accept with higher premium - Accept with higher deductible - Decline coverage

## 9. Frequency vs. Severity Strategy
- **Area E/F** affects frequency → focus on      safe-driving incentives.
- **Region Corse** affects severity → focus on      higher deductibles or repair-cost controls.

## 10. Management Reporting

Present findings to management: BonusMalus, DrivAge, VehAge, Area, and Region are the key drivers. Recommend pricing adjustments and portfolio rebalancing.
