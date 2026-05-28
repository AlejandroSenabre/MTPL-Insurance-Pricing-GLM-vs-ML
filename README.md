# Motor Third-Party Liability (MTPL) Insurance Pricing: GLM vs. Machine Learning

> 📢 **Nota del Autor:** Este repositorio y su documentación están redactados íntegramente en inglés con el objetivo pedagógico de practicar y consolidar la escritura técnica en este idioma, alineándome con los estándares globales de la práctica actuarial y la ciencia de datos.

## 📌 Project Overview
This repository implements an end-to-end actuarial pricing pipeline for Motor Third-Party Liability (MTPL) insurance, using the classic French portfolio datasets (`freMTPL2freq` and `freMTPL2sev`). 

The goal of this project is to develop a mathematically robust and commercially viable **Pure Premium (Tarifa Técnica)** framework, comparing traditional actuarial methodologies against modern Machine Learning approaches.

## 🛠️ Data Engineering & Actuarial Adjustments
Before modeling, a strict data preparation pipeline was built (`src/data_preparation.py`) to enforce actuarial consistency and protect the models from statistical noise:
* **Exposure Control:** Excluded policies with trivial exposure ($Exposure \le 0.005$) to eliminate cancellation noise and prevent artificial frequency spikes.
* **Feature Binning:** Transformed highly non-linear risk predictors—such as Driver Age (`DrivAge`) and Vehicle Age (`VehAge`)—into categorical risk brackets to stabilize coefficients.
* **Outlier Truncation (Capping):** Handled heavy-tailed distributions by capping `BonusMalus` at 130, `VehPower` at 9, and extreme insurance claims at the **99.5th percentile** to stabilize mean severity estimation.
* **Categorical Encoding:** Retained geographical `Area` factors in their raw form, allowing the statistical framework to dynamically handle dummy variables while avoiding the perfect multicollinearity trap.

## 🔬 Predictive Modeling Framework

### 1. Frequency Model (Poisson GLM)
* **Distribution:** Poisson family with a `log` link function.
* **Exposure Integration:** Utilized $log(Exposure)$ as an **offset** to model the annualized claim arrival rate ($\lambda$).
* **Key Finding:** Successfully identified severe multicollinearity between raw population density and geographical zones, resulting in an optimized spatial risk structure. `BonusMalus` and `Is_Diesel` emerged as top predictive drivers.

### 2. Severity Model (Gamma GLM)
* **Distribution:** Gamma family with a `log` link function, trained exclusively on policies with strictly positive claims ($ClaimNb > 0$).
* **Key Finding:** Demonstrated that risk drivers behave differently across models. While young drivers (18-24) present higher claim frequencies, they also drive the highest mean severity (€2,059.44), proving that younger cohorts cause significantly more expensive accidents compared to older, stabilized brackets.

## 📈 Financial & Portfolio Evaluation (Test Set Results)
Evaluating the multiplicative combination of both models ($\text{Pure Premium} = \hat{\lambda} \times \hat{Z}$) on an unseen 20% validation split yielded highly accurate portfolio matching:
* **Total Actual Claims:** € 9,259,380.45
* **Total Predicted Pure Premium:** € 8,802,631.83
* **Portfolio Coverage Ratio:** **95.07%**

*Note: In production environments, this minor variance is adjusted via an actuarial re-baselining factor (~1.051) to ensure 100% financial solvency.*

## 🚀 Next Steps
* [x] Exploratory Data Analysis (EDA) & Cleaning
* [x] Data Preparation Pipeline & Automated Feature Engineering
* [x] GLM Poisson Frecuency & Gamma Severity Calibration
* [ ] **In Progress:** Training Machine Learning benchmarks (XGBoost / LightGBM) and comparing pricing performance via Gini Indices and Lorenz Curves.
