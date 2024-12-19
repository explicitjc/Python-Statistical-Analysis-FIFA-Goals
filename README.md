## Description
This project is an analysis of FIFA World Cup match results using Python, comparing the number of goals scored in men's and women's international soccer matches.

# FIFA World Cup Goal Analysis: Men vs Women

## Description
This project is an analysis of FIFA World Cup match results, comparing the number of goals scored in men's and women's international soccer matches. The goal of the project is to test the hypothesis:

- **Null Hypothesis (H0):** The mean number of goals scored in women's international soccer matches is the same as in men's.
- **Alternative Hypothesis (Ha):** The mean number of goals scored in women's international soccer matches is greater than in men's.

The analysis uses official FIFA World Cup data from both men's and women's competitions since 2002. The project aims to answer whether there is a statistically significant difference in the number of goals scored in the matches.

## Context
You're working as a sports journalist at a major online sports media company, specializing in soccer analysis and reporting. You've been watching both men's and women's international soccer matches for a number of years, and your gut instinct tells you that more goals are scored in women's international football matches than men's. This would make an interesting investigative article that your subscribers are bound to love, but you'll need to perform a valid statistical hypothesis test to be sure!

While scoping this project, you acknowledge that the sport has changed a lot over the years, and performances likely vary a lot depending on the tournament. So, you decide to limit the data used in the analysis to only official FIFA World Cup matches (not including qualifiers) since 2002-01-01. You create two datasets containing the results of every official men's and women's international football match since the 19th century, which you scraped from a reliable online source. This data is stored in two CSV files: `women_results.csv` and `men_results.csv`. 

The question you are trying to determine the answer to is: **Are more goals scored in women's international soccer matches than men's?** You assume a 10% significance level, and use the following null and alternative hypotheses: 

- **H0**: The mean number of goals scored in women's international soccer matches is the same as men's.
- **Ha**: The mean number of goals scored in women's international soccer matches is greater than men's.

## Steps Involved

## 1. Data Collection
The data is collected from official sources and consists of match results for both men's and women's FIFA World Cup competitions. The dataset includes match dates, home and away scores, and other related details.

## 2. Data Preprocessing
The following preprocessing steps were carried out:
- Filtered data to only include official FIFA World Cup matches from 2002 onwards.
- Calculated the total number of goals scored in each match by summing home and away scores.

```python
import pandas as pd

# Load the data
men_df = pd.read_csv('men_results.csv')
women_df = pd.read_csv('women_results.csv')

# Filter data for FIFA World Cup matches after 2002-01-01
men_wc_df = men_df[(men_df['tournament'] == 'FIFA World Cup') & (men_df['date'] >= '2002-01-01')]
women_wc_df = women_df[(women_df['tournament'] == 'FIFA World Cup') & (women_df['date'] >= '2002-01-01')]

# Calculate total goals for each match
men_wc_df['total_goals'] = men_wc_df['home_score'] + men_wc_df['away_score']
women_wc_df['total_goals'] = women_wc_df['home_score'] + women_wc_df['away_score']
````


## 3. Exploratory Data Analysis (EDA)
Exploratory Data Analysis was conducted to:

- Visualize the distribution of total goals scored in men’s and women’s FIFA World Cup matches.
- Check for normality in the distribution of goals using statistical tests.

```python
import seaborn as sns
import matplotlib.pyplot as plt
from scipy.stats import shapiro

# Visualize the distributions
sns.histplot(men_wc_df['total_goals'], kde=True, label='Men', color='blue', bins=10)
sns.histplot(women_wc_df['total_goals'], kde=True, label='Women', color='red', bins=10)
plt.legend()
plt.title("Distribution of Total Goals in Men's and Women's FIFA World Cup Matches")
plt.xlabel("Total Goals per Match")
plt.ylabel("Frequency")
plt.show()

# Check for normality using the Shapiro-Wilk test
men_normality = shapiro(men_wc_df['total_goals'])
women_normality = shapiro(women_wc_df['total_goals'])

normality_results = {
    "men_p_val": men_normality.pvalue,
    "women_p_val": women_normality.pvalue,
    "men_normal": men_normality.pvalue > 0.10,
    "women_normal": women_normality.pvalue > 0.10
}

print(normality_results)
````
## 4. Hypothesis Testing
The hypothesis test used to compare the means of goals scored in men's and women's matches is based on:

- **Null Hypothesis (H0):** The mean number of goals scored in women's matches is the same as in men's.
- **Alternative Hypothesis (Ha):** The mean number of goals scored in women's matches is greater than in men's. A two-sample t-test or Mann-Whitney U test was used depending on the normality of the data:

```python
from scipy.stats import ttest_ind, mannwhitneyu

alpha = 0.10
if not (normality_results["men_normal"] and normality_results["women_normal"]):
    # Perform a one-tailed Mann-Whitney U test (alternative='greater')
    u_stat, p_val = mannwhitneyu(women_wc_df['total_goals'], men_wc_df['total_goals'], alternative='greater')
    test_used = "Mann-Whitney U test"
else:
    # If data is normal, revert to Welch's t-test
    t_stat, p_val = ttest_ind(women_wc_df['total_goals'], men_wc_df['total_goals'], equal_var=False, alternative='greater')
    test_used = "Welch's t-test"

result = "reject" if p_val < alpha else "fail to reject"
result_dict = {
    "p_val": p_val,
    "result": result
}

print(f"Test Used: {test_used}")
print(f"Result: {result_dict}")'''
````

## 5. Statistical Results
**The results from the statistical test yielded the following:**

- **P-value:** The p-value determines the significance of the test.
**Decision:** Based on the p-value, we either reject or fail to reject the null hypothesis at a 10% significance level. The p-value from the statistical test determines the significance of the difference in mean goals scored in men's and women's FIFA World Cup matches. If the p-value is less than 0.10 (the chosen significance level), we reject the null hypothesis, implying that the mean number of goals scored in women's matches is significantly higher than in men's. If the p-value is greater than 0.10, we fail to reject the null hypothesis, implying that there is no significant difference in the mean number of goals scored between men's and women's matches.

## 6. Conclusion
The outcome of the test helps determine whether there is a statistically significant difference in the number of goals scored between men's and women's FIFA World Cup matches. The conclusion is based on whether we reject or fail to reject the null hypothesis.

Based on the statistical analysis and the p-value, we conclude whether there is a significant difference in the mean number of goals scored in men's and women's FIFA World Cup matches.

This project uses data sourced from [DataCamp](https://www.datacamp.com/) for educational and learning purposes. All intellectual property rights related to the dataset belong to DataCamp. This analysis is strictly for personal portfolio development and non-commercial use.
