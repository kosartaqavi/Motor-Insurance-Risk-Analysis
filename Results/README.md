# Results

This folder contains the main outputs of the statistical models and analysis.

Included results:
- Frequency model results
- Severity model comparison
- Model validation metrics
- Expected loss calculations
- Risk factor analysis


📊 Key Risk Factors & Business Recommendations
🔍 Which Factors Have the Highest Impact on Expected Loss?
Based on drop1() analysis and summary() coefficients from both frequency (Poisson) and severity (Lognormal) models:

Factor	Frequency Impact (LRT)	Severity Impact (F)	Overall Impact	Direction
⭐ BonusMalus	Strongest (4388.8)	Strong (211.0)	Highest	Increases Expected Loss
⭐ DrivAge	Strong (265.6)	Strong (79.6)	High	Increases (U-shape)
⭐ VehAge	Strong (1156.2)	Moderate (5.7)	High	Decreases
VehBrand	Moderate (138.7)	Moderate (7.1)	Moderate	Varies by brand
Area	Moderate (147.8)	Not significant	Frequency-driven	Increases in E, F
Region	Moderate (172.0)	Weak (1.84)	Moderate	Varies by region
⚠️ Highest-Risk Levels
BonusMalus = 230 → Expected Loss = 5,810 (165× higher than BonusMalus = 51)

Area F → +26.05% Expected Loss vs Area A (frequency-driven)

Area E → +25.55% Expected Loss vs Area A (frequency-driven)

Region Corse → +48.54% Expected Loss vs reference (severity-driven)

DrivAge 18–25 and 80+ → U-shaped, both ends have higher Expected Loss

VehAge = 1 (newest vehicles) → higher Expected Loss than older vehicles

VehBrand B12 → +41.47% Expected Loss vs Brand B1

💡 Business Recommendations
1️⃣ Risk-Based Pricing
Set premiums proportional to Expected Loss. Policies like IDpol 141668 (Expected Loss = 5,810) should pay significantly higher premiums. Low-risk policies (Expected Loss ≈ 50) can receive discounts.

2️⃣ Risk Segmentation
Classify policies into Low / Medium / High risk groups. Offer standard pricing to Low and Medium, and apply stricter underwriting to High-risk policies.

3️⃣ BonusMalus Review
Since BonusMalus is the strongest predictor of both frequency and severity, the insurer should review the BonusMalus system to ensure it accurately reflects risk. Policies with BonusMalus > 150 should be flagged.

4️⃣ Geographic Strategy
Area E and F: Premiums should be adjusted upward for higher claim frequency.

Region Corse: Premiums should be adjusted upward for higher claim severity (higher repair costs).

5️⃣ Deductible Design
For high-risk groups (elderly drivers 80+, BonusMalus > 150, Area F), offer policies with higher deductibles to reduce claim severity exposure.

6️⃣ Portfolio Management
Balance the portfolio by mixing Low and High risk policies. Avoid over-concentration in high-risk segments (Area F, Corse, high BonusMalus).

7️⃣ Monitoring & Early Warning
Flag policies with predicted_claims > 1.5 or expected_loss > 3,000 for manual review before renewal.

8️⃣ Underwriting Decisions
For very high-risk policies (Expected Loss > 5,000), consider:

✅ Accept with higher premium

✅ Accept with higher deductible

❌ Decline coverage

9️⃣ Frequency vs. Severity Strategy
Area E/F affects frequency → focus on safe-driving incentives.

Region Corse affects severity → focus on higher deductibles or repair-cost controls.

🔟 Management Reporting
Present findings to management: BonusMalus, DrivAge, VehAge, Area, and Region are the key drivers. Recommend pricing adjustments and portfolio rebalancing.

📌 Key Takeaway
BonusMalus is the strongest driver of Expected Loss. Area E/F and Region Corse are high-risk zones. The insurer should implement risk-based pricing, risk segmentation, and geographic strategy to manage these risks effectively.

