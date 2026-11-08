# Diabetes Risk Factors: Exploratory Analysis in R

An exploratory data analysis of the Pima Indians Diabetes dataset in R. The project cleans the data, compares diabetic and non-diabetic patients visually and statistically, and fits a logistic regression to see which measurements are associated with diabetes.

Author: Devansh Singh

## Question

Which clinical measurements best distinguish patients with diabetes from those without?

## Dataset

- 768 female patients, 8 measurements plus an outcome (`Outcome`: 0 = no diabetes, 1 = diabetes)
- 500 patients without diabetes, 268 with diabetes
- Variables: Pregnancies, Glucose, BloodPressure, SkinThickness, Insulin, BMI, DiabetesPedigreeFunction, Age
- Source: Pima Indians Diabetes Database, originally from the National Institute of Diabetes and Digestive and Kidney Diseases (available on UCI and Kaggle). The file is included in `data/` for convenience.

## Method

1. **Cleaning.** In Glucose, Blood Pressure, Skin Thickness, Insulin and BMI, a value of 0 is biologically impossible and really means "not measured". These zeros were converted to `NA`. After this step, Insulin is missing for 374 patients (about 49%) and Skin Thickness for 227 (about 30%).
2. **Exploration.** Histogram, boxplot, scatter plot, correlation heatmap and density plot.
3. **Group comparison.** Welch t-tests for Glucose and BMI; Wilcoxon rank-sum test for Insulin (skewed).
4. **Modelling.** Logistic regression: `Outcome ~ Glucose + BMI + Age`.

## Key findings

- **Glucose separates the groups best.** Median glucose is 107 in patients without diabetes and 140 in patients with diabetes (means 110.6 vs 142.3). The difference is highly significant (Welch t-test, p < 0.001).
- **BMI and insulin are also higher in the diabetes group.** Mean BMI is 30.9 vs 35.4 (p < 0.001). Median insulin is 102.5 vs 169.5 (Wilcoxon, p < 0.001). The groups overlap much more in BMI than in glucose.
- **Insulin is strongly right-skewed**, so medians and rank-based tests are more appropriate than means and t-tests.
- **Correlations between measurements:** Pregnancies and Age are correlated (about 0.55), as are Glucose and Insulin (about 0.58).
- **Logistic regression** (752 patients with complete data for the three predictors) gave these odds ratios, all with p < 0.001:

| Predictor | Odds ratio (per 1-unit increase) |
|---|---|
| Glucose | 1.04 |
| BMI | 1.09 |
| Age | 1.03 |

Higher glucose, BMI and age were each associated with higher odds of diabetes when the other two were held constant. Odds ratios are per one unit of each variable, so their sizes should not be compared directly across predictors.

## Figures

After running the script, the plots are saved in `results/`:

| File | Shows |
|---|---|
| `insulin_histogram.png` | Distribution of insulin (zeros removed) |
| `glucose_boxplot.png` | Glucose by outcome |
| `glucose_vs_bmi.png` | Glucose vs BMI, coloured by outcome |
| `correlation_heatmap.png` | Correlation between measurements |
| `glucose_density.png` | Glucose density curves by outcome |
| `model_summary.txt` | Logistic regression output and odds ratios |

## How to run

1. Install [R](https://cran.r-project.org/) and [RStudio](https://posit.co/download/rstudio-desktop/).
2. Download or clone this repository.
3. Open `analysis.R` in RStudio, set the working directory to the project folder (Session > Set Working Directory > To Source File Location).
4. Run the whole script (Ctrl+Shift+Enter). Required packages (`readxl`, `ggplot2`) install automatically if missing.

## Limitations

- Small dataset (768 patients) from a single population, so results may not generalise.
- Heavy missingness in Insulin and SkinThickness; complete-case analysis drops patients and may introduce bias.
- This is an association study, not a causal one. The model was not evaluated on held-out data, so it makes no claim about predictive accuracy.
- Only three predictors were modelled; other variables (e.g. Pregnancies, DiabetesPedigreeFunction) could be added and compared.

## Possible next steps

- Add more predictors and compare models with AIC.
- Split into training and test sets and report accuracy / ROC-AUC.
- Impute missing values instead of dropping them.

## Tools

R, `readxl`, `ggplot2`, base R graphics (`hist`, `boxplot`, `heatmap`), `glm`.


## Figures

### Glucose by outcome
![Glucose boxplot](results/glucose_boxplot.png)
*Glucose is clearly higher in patients with diabetes (median 140 vs 107), and the boxes barely overlap.*

### Glucose distribution by outcome
![Glucose density](results/glucose_density.png)
*The diabetes curve is shifted right with a long tail toward 200; the overlap shows glucose is strong but not a perfect separator.*

### Glucose vs BMI
![Glucose vs BMI](results/glucose_vs_bmi.png)
*Diabetic patients (red) sit higher on glucose, while BMI overlaps heavily between groups, so glucose carries more information.*

### Correlation between measurements
![Correlation heatmap](results/correlation_heatmap.png)
*Darker squares mean stronger correlation; the strongest links are Glucose-Insulin, Pregnancies-Age and BMI-SkinThickness.*

### Insulin distribution
![Insulin histogram](results/insulin_histogram.png)
*Insulin is strongly right-skewed and missing for about 49% of patients, so a rank-based test was used.*

### Logistic regression
See [`results/model_summary.txt`](results/model_summary.txt): glucose, BMI and age were each associated with higher odds of diabetes (odds ratios 1.04, 1.09 and 1.03, all p < 0.001).

