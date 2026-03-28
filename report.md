# Introduction

This project analyzes the Ames Housing dataset, which contains 2,930 house sales in Ames, Iowa, and more than 80 features describing each property. These features include information about size, quality, condition, and sale price, making the dataset useful for understanding what affects house prices.

The main goal of this project is to explore the factors that influence `SalePrice` and identify the most important patterns in the data. The analysis focuses on questions such as:

- Which features are most strongly related to `SalePrice`?
- How do quality and living area affect price?
- What clear patterns can be found in the dataset?

To answer these questions, the project uses data cleaning, exploratory analysis, and feature engineering. A log transformation was also applied to `SalePrice` to reduce skewness and make the distribution easier to analyze.

---

# Cleaning Summary

The dataset had several issues that needed to be fixed before analysis. These included missing values, incorrect data types, and outliers in the target variable.

**Data types:**  
`Bsmt Full Bath` and `Bsmt Half Bath` were stored as `float64`, even though they represent counts. These columns were filled with 0 where needed and converted to `int64`.

**Missing values:**  
Different strategies were used depending on the column:

- `Lot Frontage` was filled with the median because it is less affected by outliers.
- `Electrical` was filled with the mode because it had only one missing value.
- Columns with many missing values, such as `Alley`, `Pool QC`, `Fence`, and `Misc Feature`, were not dropped because the missing values were meaningful. They show that most houses simply do not have those features. These were filled with `"Missing"` for categorical columns and `0` for numerical columns.

**Duplicates:**  
No duplicate rows were found.

**Outliers:**  
Outliers in `SalePrice` were identified using the IQR method. Instead of removing them, the extreme values were capped at the 99th percentile so the data could stay complete while reducing the effect of very large prices.

A reusable `clean_data()` function was created to apply all cleaning steps in a consistent way. Validation checks confirmed that:

- there are no missing values in key columns such as `SalePrice`, `Overall Qual`, and `Gr Liv Area`
- all `SalePrice` values are greater than 0
- the dataset still contains 82 columns

---

# Feature Engineering Summary

After cleaning, several new features and transformations were added to make the dataset more useful for analysis.

**One-hot encoding:**  
`Land Slope` and `Garage Finish` were converted into binary columns using `pd.get_dummies()`.

**Ordinal encoding:**  
`Kitchen Qual` was encoded as an ordered variable from 1 to 5, from Poor to Excellent.

**Scaling:**  
`Lot Area` and `Gr Liv Area` were standardized using `StandardScaler` so they would be on the same scale.

**Domain features:**  
Two useful ratio features were created:

- `price_per_sqft`: sale price divided by living area
- `avg_room_size`: living area divided by total rooms above ground

**Interaction feature:**  
`qual_area_int` was created by multiplying `Overall Qual` by `Gr Liv Area`. This captures the combined effect of size and quality.

**Log transformation:**  
`SalePrice` was transformed using `np.log1p()` to reduce skewness and make the distribution more balanced.

**Binning:**  
`Year Built` was grouped into three categories: `Old`, `Recent`, and `New`. This makes it easier to compare homes from different construction periods.

---

# Key Findings

**1. Missing values often represent real property features**  
Some columns had very high missing values, such as `Alley`, `Pool QC`, and `Misc Feature`. These were not errors. They mostly mean that the feature does not exist for many houses. Keeping them as `"Missing"` preserved useful information.

**2. Quality and size together are important**  
The interaction feature `qual_area_int` shows that house value depends on both quality and living area. A large house with poor quality and a smaller house with excellent quality can have similar prices, so the combination of both features matters more than either one alone.

**3. High-quality homes are much more likely to have above-average prices**  
The probability analysis showed that houses with `Overall Qual` of 7 or higher had a 90.09% chance of being above the mean `SalePrice`. This makes overall quality one of the strongest indicators of house value.

The correlation analysis also supported this result. `Overall Qual` had the strongest correlation with `SalePrice` at 0.83, followed by `avg_room_size` at 0.71 and `Gr Liv Area` at 0.69.

---
![Correlation Heatmap](figures\correlation_heatmap.png)

---

# What I Would Do Next

The next step would be to build predictive models for `SalePrice`.

- Train a **Linear Regression** model as a baseline
- Try a **Random Forest** model to capture more complex relationships
- Evaluate both models using **RMSE**
- Use **cross-validation** to check that the results are stable

---