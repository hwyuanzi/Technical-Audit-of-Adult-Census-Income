# Technical Audit of Adult Census Income

This repository contains a Responsible Data Science technical audit of an Adult Census Income Algorithmic Decision-Support System (ADS). The project was completed for the Responsible Data Science course at New York University in Spring 2026.

Authors:

- [Hollan Yuan](https://github.com/hwyuanzi)
- [Sylvia Zhang](https://github.com/Sylvia-0728)

## Project Background

The audited ADS predicts whether a person's annual income is greater than `$50K` or less than or equal to `$50K` using the Adult Census Income dataset. The dataset contains 32,561 records extracted from the 1994 U.S. Census Bureau database and includes demographic, education, work, and financial features such as age, workclass, education, marital status, occupation, race, sex, capital gain, capital loss, hours worked per week, native country, and income.

The `$50K` threshold is not a value chosen by this project. It is the dataset's original binary target label. In this audit, `$50K` should therefore be understood as a historical benchmark label from the 1994 dataset, not as a universal or current definition of wealth.

Income prediction can be socially consequential because income is often used as a proxy for socioeconomic status in research, policy analysis, marketing, resource allocation, and other decision-support contexts. A model with strong overall accuracy can still create unequal harm if it misclassifies some groups more often than others. For that reason, this project asks not only whether the model is accurate, but also whose outcomes it predicts well, whose outcomes it misses, and whether error patterns are concentrated across meaningful social groups.

## Project Purpose

The goal of this project is to audit the original Adult Census Income modeling workflow from a Responsible Data Science perspective. Rather than treating the notebook as only a machine-learning exercise, we evaluate the ADS as a system that could influence real-world decisions.

The audit focuses on:

- Input and output profiling, including hidden missing values encoded as `?`
- The original implementation and validation strategy
- Subgroup performance across sex, race, workclass, age, marital status, and education
- Group fairness metrics, including statistical parity, equal opportunity, false positive rate gaps, and false negative rate gaps
- Intersectional fairness across combined attributes such as race x sex, race x education, and marital status x sex
- The failure of "fairness through unawareness" when sensitive attributes are dropped but proxy variables remain
- Imputation strategy sensitivity and the effect of treating missing values as `Unknown`
- Robustness of the original age-based filtering choice
- Transparency through Logistic Regression coefficients and linear contribution analysis
- Uncertainty through conformal prediction and subgroup coverage
- Privacy risk through quasi-identifier uniqueness and differential privacy discussion

## What This Project Audits

The original notebook trains a Logistic Regression classifier to predict whether income is `>50K` or `<=50K`. The model reports about 85% global accuracy, but the audit shows that this headline metric hides important problems:

- The target label is imbalanced: about 75.92% of records are `<=50K`, while 24.08% are `>50K`.
- Hidden missing values appear in `workclass`, `occupation`, and `native.country`.
- The original workflow removes `race`, which does not guarantee race-neutral outcomes because other variables can act as proxies.
- The original workflow mode-imputes hidden `?` values, which can hide meaningful missingness.
- The original workflow applies an IQR age filter that removes 166 older individuals without improving model quality.
- The confusion matrix shows asymmetric errors: false negatives are more than twice false positives, meaning many true `>50K` individuals are predicted as `<=50K`.

## Main Findings

The audit finds that the ADS is useful as an educational benchmark, but it is not ready for high-stakes deployment.

Key findings include:

- Aggregate accuracy is misleading. Women have higher overall accuracy than men, but lower recall and higher false negative rates for the `>50K` class.
- Race-based disparities remain even when the `race` feature is removed from training.
- Education produces the largest subgroup fairness gaps. The model strongly favors formal education signals and often misses high-income individuals outside that profile.
- Intersectional groups can experience much higher false negative rates than single-attribute summaries reveal.
- Proxy analysis shows that variables such as `native.country`, `sex`, `relationship`, `marital.status`, `occupation`, and `education` retain association with race.
- Treating missing values as an explicit `Unknown` category is more transparent than replacing them with majority categories.
- Removing the original age filter slightly improves model performance and avoids excluding older individuals.
- Conformal prediction gives about 94.8% overall coverage, but coverage falls below the 95% target for some subgroups.
- The privacy audit shows that many records are highly specific: 36.8% are unique under the quasi-identifier combination of age, education, marital status, occupation, race, sex, and native country.

## Recommendations

Based on the report, notebook, and presentation slides, this project recommends:

- Treat hidden `?` values as an explicit `Unknown` category instead of silently mode-imputing them.
- Remove or clearly justify the age-based IQR filter.
- Report subgroup metrics instead of relying only on global accuracy.
- Retain sensitive attributes for internal fairness auditing, while protecting them from unnecessary exposure.
- Report conformal prediction coverage by subgroup, not only in aggregate.
- Generalize rare quasi-identifier combinations before any public data release.
- Consider differential privacy if model outputs, coefficients, or aggregate statistics are released publicly.
- Do not deploy this ADS in public-sector or high-stakes industry settings without stronger fairness, privacy, uncertainty, and documentation safeguards.

## Repository Structure

```text
.
|-- README.md
|-- LICENSE
|-- .gitignore
|-- adult.csv
|-- adult-census-income.ipynb
|-- Report.pdf
`-- Presentation Slides.pdf
```

## File Guide

| File | What it is | How to use or open it |
| --- | --- | --- |
| `README.md` | The main project guide. It explains the project background, purpose, findings, file structure, and usage instructions. | Open directly on GitHub or in any text/Markdown editor. |
| `LICENSE` | The repository license file. | Open directly on GitHub or in any text editor. |
| `.gitignore` | Git configuration for files that should not be tracked. | Usually no action is needed; it is mainly for repository maintenance. |
| `adult.csv` | The Adult Census Income dataset used by the notebook and audit. It has 32,561 data rows and 15 columns, including the `income` target label. | Use as the input dataset when running the notebook. Keep the filename as `adult.csv` for the easiest setup. |
| `adult-census-income.ipynb` | The main Google Colab / Jupyter notebook. Part I reproduces the original Adult Census Income modeling workflow. Part II contains the Responsible Data Science audit, including subgroup metrics, fairness gaps, imputation sensitivity, age-filter robustness, transparency analysis, conformal prediction, and privacy audit. | Open in Google Colab or Jupyter Notebook. See the running instructions below. |
| `Report.pdf` | The full written technical audit report. It explains the background, input/output analysis, implementation and validation critique, outcome metrics, fairness analysis, robustness checks, conformal prediction, privacy risks, and final recommendations. | Open with any PDF reader, or click the file on GitHub and use the browser preview/download option. |
| `Presentation Slides.pdf` | A slide-deck summary of the project. It presents the audit motivation, dataset structure, implementation red flags, subgroup findings, fairness metrics, proxy analysis, robustness, transparency, conformal prediction, privacy risk, and recommendations. | Open with any PDF reader, or click the file on GitHub and use the browser preview/download option. |

## How to Run the Notebook in Google Colab

1. Open `adult-census-income.ipynb` in Google Colab.
2. Upload `adult.csv` to the Colab runtime, or place it in Google Drive as `/content/drive/MyDrive/adult.csv`.
3. Run the notebook from top to bottom.
4. If Colab asks for Google Drive authorization, approve it only if you are using the Drive path.
5. The audit section automatically searches common paths such as `adult.csv`, `adult(1).csv`, `/content/adult.csv`, `/content/drive/MyDrive/adult.csv`, and `/mnt/data/adult.csv`.

When the audit cells run, they may generate an `audit_outputs/` folder containing CSV tables for metrics, fairness gaps, imputation comparisons, coefficient analysis, conformal coverage, and privacy summaries.

## How to Run the Notebook Locally

Create and activate a Python environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
python3 -m pip install numpy pandas matplotlib seaborn scikit-learn jupyter ipython
```

Then start Jupyter:

```bash
jupyter notebook adult-census-income.ipynb
```

Local notes:

- Keep `adult.csv` in the same folder as the notebook.
- The first Colab-specific cell mounts Google Drive; skip or comment out that cell when running locally.
- In Part I, if a cell reads from `/content/drive/MyDrive/adult.csv`, change that path to `adult.csv`.
- Part II has a more robust dataset loader and should automatically find `adult.csv` in the repository folder.
- Optional SHAP code is present but disabled in the notebook; the main transparency audit uses Logistic Regression coefficients and linear contribution tables.

## Data and Source References

- ADS notebook audited: <https://www.kaggle.com/code/sanjay7013/adult-census-income/notebook>
- Dataset: <https://www.kaggle.com/datasets/uciml/adult-census-income>
- Original dataset context: Adult Census Income dataset extracted from the 1994 Census Bureau database by Ronny Kohavi and Barry Becker.

## Project Status

This repository is an academic audit project, not a deployable income prediction product. The model and dataset are used to evaluate responsible data science concerns such as fairness, robustness, transparency, uncertainty, and privacy risk.
