# Data Cleaning Pipeline

## 1. Original Dataset

I wanted to build a data cleaning pipeline, so I picked a messy [dataset from Kaggle](https://www.kaggle.com/datasets/terencekatua/employee-pay-equity-dataset).

The dataset contains **96,900 records and 19 columns** from three different HR sources.

Before cleaning anything, I explored the data using my EDA app I developed for this kind of work.

<img width="800" height="599" alt="demo" src="https://github.com/user-attachments/assets/ab848f88-4c04-47a0-aa52-6fd3333f3e81" />


    

The first interesting issue was **duplicate records**. Initially, all employee IDs appeared to be unique, so a standard duplicate check didn't reveal anything. After looking closer, I noticed some IDs ending with `_DUP`. There were actually **1,900 duplicate employee records** hidden this way.

Another issue was inconsistent text labels. I reviewed the unique-value counts and frequency distributions across all text columns, including those with high cardinality. Some columns had different labels for exactly the same thing. For example, gender had 15 different labels representing only 5 categories, while department had 24 labels for 12 departments. Similar issues existed in country names, education levels and other categorical columns.

Dates were another problem. Hire dates were stored in **5 different formats**, so I standardized them into a single format (`YYYY-MM-DD`).

There were also data type issues. I found **32,950 salary values** with these additional characters. After checking the formats and currency information, I converted the column into a numerical format. Since salaries were reported in 12 different currencies, I also needed a common currency for comparison.

And of course, there were **missing values**. The original dataset contained **67,549 missing values across 9 columns**. Some could be recovered from existing information, while others required further investigation.

I fixed what I could, didn't make unnecessary assumptions and kept track of anything that still needs attention.

## 2. Cleaning Results

### Duplicate Records

- Identified and removed **1,900 duplicate records**.
- Compared duplicate records with their original employee records.
- Recovered **76 missing values** using information from matching duplicates.
- Preserved all original employee IDs and existing non-null values.

**Result: 95,000 unique employee records.**

### Missing Values

After duplicate removal, the dataset contained 66,131 missing values.

- Recovered **931 missing tenure values** using hire dates.
- Estimated **2,839 missing ages** using the median method.
- Estimated **899 missing experience values** using KNN.
- Generated **14,758 performance review score estimates** using Random Forest.

Predictions were stored in separate columns rather than replacing missing values.

Prediction methods were also tested for remote work status and education level. Their accuracy was insufficient, so missing values were preserved.

Missing gender and ethnicity values were also preserved to avoid introducing unverified demographic information.

### Data Standardization

The following improvements were made:

| Column | Before | After |
|---|---|---|
| Gender | 15 labels | 5 categories |
| Department | 24 labels | 12 departments |
| Country | 17 labels | 14 countries |
| Education | 10 labels | 5 categories |
| Performance Rating | 5 labels | 3 categories |
| Remote Status | 8 labels | 3 categories |
| Job Title | 2,856 records with unnecessary whitespace | Whitespace standardized |
| Hire Date | 5 date formats | YYYY-MM-DD |

Numerical, text, date and categorical columns were converted to appropriate data types.

### Salary Validation

- Converted salaries from **12 currencies into USD** using predefined exchange rates.
- Preserved original salary amounts and currencies.
- Flagged **234 salaries** for further review:
  - 113 below $1,000 USD.
  - 121 above $1,000,000 USD.

These values were flagged as potential anomalies, not confirmed errors.

### Data Integrity

Final validation confirmed:

- No duplicate employee IDs.
- No missing or invalid employee IDs.
- No missing or future hire dates.
- No inconsistencies between calculated and recorded tenure.
- No detected logical conflicts between age, experience and tenure where sufficient information was available.

Statistical outliers were reviewed without automatically removing or modifying valid records.

## 3. Final Dataset

| Metric | Before | After |
|---|---:|---:|
| Employee Records | 96,900 | 95,000 |
| Columns | 19 | 35 |
| Duplicate Employee Records | 1,900 | 0 |
| Missing Values* | 66,207 | 65,200 |
| Missing Tenure Values | 931 | 0 |
| Salaries Flagged for Review | Not flagged | 234 |

*Missing values are compared across the original 19 columns for the same 95,000 employees. This excludes removed duplicate records and additional tracking columns.*

### Why did the Column Count Increase?

The dataset increased from **19 to 35 columns** because 16 additional columns were created:

- **9 missing-value flags** to track originally missing information.
- **3 prediction columns** for age, experience and performance scores.
- **1 performance rating ordinal column**.
- **1 job level column** derived from job titles.
- **1 salary column in USD** for comparisons across currencies.
- **1 salary review flag** to identify unusual values.

These additional columns improve data usability and transparency without overwriting the original employee information. Also it might be useful for modelling.

## 4. Conclusion

The pipeline reduced the dataset from 96,900 records to **95,000 unique employees**, recovered **1,007 missing values** across duplicate removal and tenure calculation and standardized inconsistent data formats.

Uncertain predictions remain separate from actual values and unusual salaries are flagged rather than modified.

The final dataset is more consistent and ready for further analysis and modelling, while remaining data quality limitations are clearly documented.


## 5. Notebooks

I divided the cleaning process into eight notebooks. Each notebook focuses on a specific part of the pipeline.

| Notebook | Description |
|---|---|
| [01 Duplicate Detection](01_duplicate_detection_and_consolidation.ipynb) | Finding and removing duplicate records |
| [02 Missing Values](02_missing_values.ipynb) | Handling missing data and testing prediction methods |
| [03 String Formatting](03_string_formatting.ipynb) | Standardizing text, dates, and salary formats |
| [04 Data Types](04_data_types.ipynb) | Converting columns into appropriate data types |
| [05 Invalid Values](05_invalid_values.ipynb) | Checking numerical values and flagging unusual salaries |
| [06 Date Validation](06_date_validation.ipynb) | Validating hire dates and tenure |
| [07 Data Integrity](07_data_integrity.ipynb) | Checking outliers, consistency, and employee IDs |
| [08 Statistical Distributions](08_statistical_distributions.ipynb) | Reviewing numerical and categorical distributions |
