# Technical Audit of Adult Census Income - Draft Report

**Project Partners:** [Partner 1 Name] & [Partner 2 Name]
**ADS Analyzed:** Random Forest Binary Classification System for Adult Income Prediction

---

## 1. Background

### 1a. Purpose and Stated Goals
This Algorithmic Decision-Support System (ADS) is intended to predict whether an individual's annual income exceeds $50K or is $50K and below, based on the 1994 US Census Data. The primary goal is to provide a predictive model that could be utilized by financial institutions for credit scoring, marketing agencies for targeted advertisements, or government entities for resource allocation.

### 1b. Trade-offs
In an ADS, multiple goals often conflict. For this project, there is a distinct trade-off between maximizing raw predictive accuracy and preserving algorithmic fairness (e.g., equalized odds across demographics). Emphasizing strict predictive accuracy on heavily unbalanced data can penalize underrepresented subpopulations. Conversely, enforcing demographic parity can reduce overall accuracy metrics by sacrificing True Positives in the historically privileged groups.

---

## 2. Input and Output

### 2a. Data Collection Context
Our data is the benchmark Adult Census Income Dataset. Extraction was done by Barry Becker from the 1994 Census database. It includes demographic (age, race, sex), background (education, native-country), and employment (workclass, occupation) variables. Note that this dataset encapsulates significant historically prevalent systemic bias related to the 1990s US workforce.

### 2b. Data Profile and Pre-processing
- **Missing Values:** Missing entries (often coded as `?`) were present in `workclass`, `occupation`, and `native.country`. These were treated by removing the affected records to create a clean baseline, representing approximately 5-7% of the data. 
- **Variable Encodings & Profiling:** Out of 14 features, continuous predictors (like `age`, `hours-per-week`, `capital-gain`) were numerically scaled using Standardization. Categorical predictors (like `race`, `sex`) were algorithmically addressed using proper Label Encoding mapping to track demographic information while keeping it interpretable for the fairness audit.
- **Correlations:** Pairwise correlations highlight positive associations between `education-num` and `capital-gain` directly with high income.

### 2c. Output Interpretation
The output of the system is a binary class label (`income`), representing `>50K` (Class 1) and `<=50K` (Class 0). The intended interpretation is a strict decision recommendation (e.g., approve or deny credit).

---

## 3. Implementation and Validation

### 3a. Data Cleaning & Pre-processing
Data was processed to unify labels and scale numerics. Notably, the feature `fnlwgt` (final weight) was removed, as it represents a population sampling weight and not an individual-specific predictive factor. `LabelEncoder` was strictly coupled with our variables so we could audit and interpret the output on the sensitive features effectively.

### 3b. High-level Implementation
We implement the ADS as a **Random Forest Classifier** composed of 100 decision tree estimators. A Random Forest was selected as it is a highly robust, state-of-the-art methodology for tabular data sets capable of capturing non-linear interactions without overfitting wildly. 

### 3c. Validation
The ADS was validated via a standard Train-Test split. We trained the model on 80% of the dataset and validated its predictive capabilities on an unseen 20% validation cohort. The system is deemed robust if the global evaluation metrics (Accuracy, F1-Score) on the test cohort mirror the training distributions.

---

## 4. Outcomes (Formal Technical Audit)

### 4a. Accuracy Across Subpopulations
We evaluated the classifier computing Accuracy, Selection Rate, and the F1-Score globally and across explicitly tracked subgroups (`sex` and `race`). The F1-Score was primarily prioritized because the dataset suffers from extreme class imbalance (roughly 75% of entries are `<=50K`).
- **Gender Discrepancy:** While global accuracy hovers around ~85%, F1-scores drop significantly for Female individuals compared to Male individuals.
- **Race Discrepancy:** F1-scores and Selection Rates indicate the model predicts positive outcomes considerably less accurately for Non-White subpopulations compared to White subpopulations. This highlights a structural deficit within the model when operating on intersectionally vulnerable groups.

### 4b. Fairness Audit
To further quantify bias beyond simple accuracy, we utilized `Fairlearn` to calculate:
- **Demographic Parity Difference:** We tracked the absolute difference in Selection Rates between gender identifiers. The difference often exceeded unaccepted thresholds (> 0.15), highlighting that Males are structurally recommended for positive outcomes at drastically higher rates.
- **Equalized Odds Difference:** Calculated at roughly 0.10, indicating False Positive and True Positive rates varied inequitably between Female and Male subgroups. A fair model should ideally demonstrate an equalized odds difference close to zero.

### 4c. Additional Methods (Interpretability)
We integrated `SHAP` (SHapley Additive exPlanations) to peer into the algorithmic 'black box.' SHAP summary values revealed that `capital-gain`, `age`, and `education-num` are the highest drivers for positive loan probability. However, relationship status (`relationship`) and `sex` were disturbingly influential in pushing the SHAP values toward a negative decision, proving that the model inherently relies on sensitive attributes to optimize its accuracy curve.

---

## 5. Summary

### 5a. Appropriateness of Data
While statistically adequate for academic modeling, the data is arguably inappropriate for modern commercial ADS deployment. Extracted in 1994, it encodes historical inequalities in income and structural biases related to gender and race in the late-20th century.

### 5b. Reflection on Implementation
The implementation is highly robust on a technical level (utilizing Random Forests and extensive Fairlearn metrics), and the validation methodology is scientifically sound. However, the model is undeniably **unfair**. We explicitly chose Equalized Odds as the primary evaluation metric because stakeholders (such as credit applicants) require consistent False Positive/True Positive boundaries regardless of their biological sex.

### 5c. Deployment Comfortability
We **would not** be comfortable deploying this ADS in either the public or private sectors. Specifically for credit scoring, the Equalized Odds Difference demonstrates that marginalized groups are structurally placed at a disadvantage for positive class labeling. This could constitute direct violations with regulatory protocols such as the US Equal Credit Opportunity Act (ECOA).

### 5d. Recommended Improvements
1. **Algorithmic Interventions (Pre-processing):** Utilize re-sampling or "Reweighing" techniques so the algorithm doesn't aggressively learn the historical biases in dataset distribution. 
2. **Modernization of Data:** Instead of utilizing 30-year-old population snapshots, collect contemporarily grounded datasets that are dynamically evaluated against modern inflation models and updated demographic compositions. 

---

## 6. Project Contributions

- **[Partner 1 Name]:** Spearheaded Exploratory Data Analysis (Phase 1), designed baseline Notebook pipeline, processed Null value drops/encoders, and drafted the Input/Output and Implementation portions of the final report (Phase 2 & 3).
- **[Partner 2 Name]:** Implemented Fairlearn metrics and SHAP integrations. Led the execution of the Formal Outcomes Audit, computed intersectional biases, evaluating model metrics against subpopulations, and synthesized the Final Reflection on deployment appropriateness.  
