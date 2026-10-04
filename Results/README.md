# Results

This folder contains the main outputs of the statistical models and analysis.

Included results:
- Frequency model results
- Severity model comparison
- Model validation metrics
- Expected loss calculations
- Risk factor analysis


 ## 🔍 Which Factors Have the Highest Impact on Expected Loss?

Based on `drop1()` analysis and `summary()` coefficients from both the **frequency (Poisson)** and **severity (Lognormal)** models:

| Factor           | Frequency Impact (LRT) | Severity Impact (F) | Overall Impact       | Direction               |
| ---------------- | ---------------------: | ------------------: | -------------------- | ----------------------- |
| ⭐ **BonusMalus** |     Strongest (4388.8) |      Strong (211.0) | **Highest**          | Increases Expected Loss |
| ⭐ **DrivAge**    |         Strong (265.6) |       Strong (79.6) | **High**             | U-shaped                |
| ⭐ **VehAge**     |        Strong (1156.2) |      Moderate (5.7) | **High**             | Decreases               |
| **VehBrand**     |       Moderate (138.7) |      Moderate (7.1) | **Moderate**         | Varies by brand         |
| **Area**         |       Moderate (147.8) |     Not significant | **Frequency-driven** | Increases in E, F       |
| **Region**       |       Moderate (172.0) |         Weak (1.84) | **Moderate**         | Varies by region        |

---

## ⚠️ Highest-Risk Levels

* **BonusMalus = 230** → Expected Loss = **5,810**, approximately **165× higher** than BonusMalus = 51.
* **Area F** → **+26.05%** Expected Loss compared with Area A.
* **Area E** → **+25.55%** Expected Loss compared with Area A.
* **Region Corse** → **+48.54%** Expected Loss compared with the reference region, mainly driven by severity.
* **DrivAge 18–25 and 80+** → Higher Expected Loss, showing a **U-shaped relationship** with driver age.
* **VehAge = 1** → Newest vehicles show higher Expected Loss than older vehicles.
* **VehBrand B12** → **+41.47%** Expected Loss compared with Brand B1.

---

## 💡 Business Recommendations

### 1. Risk-Based Pricing

Set premiums according to estimated risk and Expected Loss. High-risk policies, such as **IDpol 141668** with an Expected Loss of approximately **5,810**, should be charged significantly higher premiums, while low-risk policies may qualify for discounts.

### 2. Risk Segmentation

Classify policies into **Low**, **Medium**, and **High** risk groups.

* **Low risk:** Standard pricing or discounts
* **Medium risk:** Standard pricing with regular monitoring
* **High risk:** Higher premiums and stricter underwriting

### 3. BonusMalus Review

Since **BonusMalus** is the strongest predictor of both claim frequency and severity, the insurer should review the BonusMalus system to ensure that it accurately reflects underlying risk.

Policies with **BonusMalus > 150** can be considered for additional risk monitoring.

### 4. Geographic Strategy

* **Areas E and F:** Consider premium adjustments due to higher claim frequency.
* **Region Corse:** Consider premium adjustments or other risk-management measures due to higher claim severity.

### 5. Deductible Design

For high-risk groups, such as:

* Drivers aged **80+**
* Policies with **BonusMalus > 150**
* Policies in **Area F**

the insurer could consider higher deductibles to reduce exposure to claim costs.

### 6. Portfolio Management

Maintain a balanced portfolio across different risk segments and avoid excessive concentration in high-risk groups, particularly those associated with:

* High BonusMalus
* Area F
* Region Corse

### 7. Monitoring & Early Warning

Flag policies with:

* `predicted_claims > 1.5`
* `expected_loss > 3,000`

for additional review, particularly at renewal.

### 8. Underwriting Decisions

For very high-risk policies with **Expected Loss > 5,000**, possible actions include:

* ✅ Accept with a higher premium
* ✅ Accept with a higher deductible
* ❌ Consider declining coverage, subject to underwriting rules and regulatory requirements

### 9. Frequency vs. Severity Strategy

Different risk factors require different management strategies:

| Risk Factor      | Main Effect          | Suggested Strategy                              |
| ---------------- | -------------------- | ----------------------------------------------- |
| **Area E/F**     | Higher frequency     | Safe-driving incentives and frequency reduction |
| **Region Corse** | Higher severity      | Higher deductibles and repair-cost controls     |
| **BonusMalus**   | Frequency + Severity | Risk-based pricing and enhanced monitoring      |
| **DrivAge**      | Frequency + Severity | Age-based risk assessment                       |

### 10. Management Reporting

Management reports should highlight the main drivers of Expected Loss:

**BonusMalus, DrivAge, VehAge, Area, and Region.**

These factors can support pricing decisions, risk segmentation, portfolio monitoring, and underwriting strategies.

---

## 📌 Key Takeaway

> **BonusMalus** is the strongest overall driver of Expected Loss, affecting both claim frequency and severity. **Area E/F** are associated with higher claim frequency, while **Region Corse** is associated with higher claim severity.
>
> The results support the use of **risk-based pricing, risk segmentation, geographic risk management, and targeted portfolio monitoring** to better manage motor insurance risk.
