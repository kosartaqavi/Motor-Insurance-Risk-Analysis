 # Motor Insurance Risk Analysis Using Statistical Models and an Analytical Dashboard

## Project Overview

**Project Objective:** Analyze factors affecting third-party motor insurance risk.

In this project, **Claim Frequency** and **Claim Severity** were modeled, and **Expected Loss** was calculated for each policy.

Combining statistical modeling in R with data analysis tools in Excel provides both a statistical and business-oriented view of insurance risk.

---

# Dataset

The dataset used in this project is the publicly available **French Motor Third-Party Liability (freMTPL2)** dataset.

**Number of records:** 678,013 policies

**Key Variables:**

* **Exposure:** The amount of time a policy is exposed to risk

* **ClaimNb:** Number of claims

* **Total Claim Amount:** Total amount of claims

* **VehPower:** Vehicle power

* **VehAge:** Vehicle age

* **DrivAge:** Driver age

* **BonusMalus:** Driver risk index

* **VehBrand:** Vehicle brand

* **VehGas:** Fuel type

* **Area and Region:** Geographical information

**Analysis Objectives:**

* Model **Claim Frequency**

* Model **Claim Severity**

* Calculate **Expected Loss** for each policy

* Identify the most influential factors affecting claim frequency and severity

# Methodology

## 1. Claim Frequency Modeling

**Objective:** Predict the expected number of claims for each policy.

**Method used:**

* **Poisson Generalized Linear Model (Poisson GLM)**


---

## 2. Claim Severity Modeling

**Objective:** Analyze the average claim amount conditional on a claim occurring.

**Models evaluated:**

* **Gamma GLM**

* **Lognormal Regression**

The models were compared using error metrics on the test dataset.

---

## 3. Expected Loss Calculation

Expected Loss was calculated as:

**Frequency × Severity**

This measure was used to estimate the expected risk associated with each policy.

---

## 4. Risk Factor Analysis

The importance of explanatory variables was assessed using the following methods:

* **Likelihood Ratio Test** for the Frequency model

* **F-test** for the Severity model

* Examination of model coefficients to interpret the direction of relationships

* Analysis of Expected Loss across different risk groups

---

# Excel Analysis and Dashboard

In addition to statistical modeling, an analytical dashboard was developed in Excel.

**Tools used:**

* Power Query

* Pivot Table

* Pivot Chart

* Power Pivot

**Dashboard features:**

* Display of key performance indicators (KPIs)

* Frequency and Severity analysis

* Analysis of different risk groups

* Interactive filtering using Slicers

**Dashboard:**

[View Dashboard](Dashboard)

---

# Tools Used

* R

* RStudio

* Excel

* Power Query

* Power Pivot

* Pivot Table

* DAX

* َِي
---

# Purpose and Application

This project focuses on the application of data science in the insurance industry and covers the main stages of an insurance risk analysis workflow, from data preparation and statistical modeling to model evaluation and results presentation.

# Project Workflow

Raw Data

↓

Preprocessing

↓

EDA & Statistical Analysis (R)

↓

Data Modeling (Power Query / Power Pivot)

↓

Interactive Excel Dashboard
