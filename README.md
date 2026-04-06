# Technical Audit of Adult Census Income

Welcome to the **Technical Audit of Adult Census Income Algorithmic Decision-Support System (ADS)** project! This work is the final project for the **Responsible Data Science** course at New York University (Spring 2026).

## Project Overview

The primary goal of this project is to perform a comprehensive technical audit of an ADS predicting whether a person's income exceeds $50K a year based on the 1994 Census database. We critically evaluate the dataset and model implementation considering crucial ethical principles such as:
- **Accuracy and Utility**
- **Algorithmic Fairness**
- **Interpretability and Transparency**
- **Robustness and Stability**

Rather than solely building a predictive model with the highest accuracy, this project assesses the real-world implications of deploying this system, scrutinizes its outcomes across different demographic subpopulations, and proposes concrete improvements for stakeholders.

## Repository Structure

- `adult_census_income.ipynb`: The main Google Colab / Jupyter Notebook containing Exploratory Data Analysis (EDA), model implementation context, and complete auditing pipelines (accuracy, fairness, interpretability).
- `adult.csv`: The Adult Census Income dataset (Extraction by Barry Becker from the 1994 Census database).
- `Raw Draft Report.docx` / `Final Report.pdf`: The detailed comprehensive report analyzing the background, inputs/outputs, implementation, outcomes, and final reflections.
- `Poster Session.pptx`: Presentation slides summarizing early takeaways and analytical plans for the class poster session.
- `Project Description - Spring 2026.pdf`: Syllabus and guidelines outlining the professor's requirements.

## Requirements & Setup

This project utilizes Python 3. We recommend using **Pipenv** to create a clean virtual environment and manage dependencies. To set up the environment and run the analysis locally, please run:

```bash
# Initialize the virtual environment and install all required libraries
pipenv install numpy pandas matplotlib seaborn scikit-learn fairlearn shap jupyter

# Activate the virtual environment
pipenv shell
```

## Dataset Information

- **Task**: Binary Classification (<=50K or >50K).
- **Features**: Age, Workclass, Fnlwgt, Education, Education-Num, Marital Status, Occupation, Relationship, Race, Sex, Capital Gain, Capital Loss, Hours per week, Native Country.
- **Source**: UCI Machine Learning Repository (Adult Dataset).

## Grading Components Addressed

This project explicitly complies with the assignment deliverables:
1. **Background**: Analyzes ADS purpose and trade-offs.
2. **Input and Output**: Profiles missing values, correlations, and feature distributions.
3. **Implementation and Validation**: Discusses existing implementation and validation rationale.
4. **Outcomes**: Audits accuracy and fairness metrics across subpopulations, extending to interpretability.
5. **Summary**: Reflects on robustness, deployment readiness, and recommends improvements.
6. **Project Contributions**: Highlights the collaborative division of work.

## Authors

- **[Hollan Yuan]**
- **[Sylvia Zhang]**