# Chapter 1: Statistics and Probability

## Table of Contents
- [Introduction](#introduction)
- [Descriptive Statistics](#descriptive-statistics)
- [Probability Basics](#probability-basics)
- [Probability Distributions](#probability-distributions)
- [Statistical Inference](#statistical-inference)
- [Hypothesis Testing](#hypothesis-testing)
- [Practical Applications](#practical-applications)

---

## Introduction

Statistics and probability form the mathematical foundation of data science and machine learning. Understanding these concepts is crucial for:
- Analyzing and interpreting data
- Making data-driven decisions
- Building and evaluating ML models
- Understanding uncertainty and variation

---

## Descriptive Statistics

###  Measures of Central Tendency

**Python:**
```python
import numpy as np
import pandas as pd
from scipy import stats

# Sample data
data = [12, 15, 18, 20, 22, 25, 28, 30, 32, 35]

# Mean (average)
mean = np.mean(data)
print(f"Mean: {mean}")  # 23.7

# Median (middle value)
median = np.median(data)
print(f"Median: {median}")  # 23.5

# Mode (most frequent)
mode_result = stats.mode(data, keepdims=True)
mode = mode_result.mode[0]
print(f"Mode: {mode}")

# Using pandas
df = pd.DataFrame({'values': data})
print(f"Mean: {df['values'].mean()}")
print(f"Median: {df['values'].median()}")
print(f"Mode: {df['values'].mode()[0]}")
```

### Measures of Dispersion

**Python:**
```python
# Variance - average squared deviation from mean
variance = np.var(data, ddof=1)  # ddof=1 for sample variance
print(f"Variance: {variance:.2f}")

# Standard deviation - square root of variance
std_dev = np.std(data, ddof=1)
print(f"Standard Deviation: {std_dev:.2f}")

# Range
data_range = max(data) - min(data)
print(f"Range: {data_range}")

# Interquartile Range (IQR)
q1 = np.percentile(data, 25)
q3 = np.percentile(data, 75)
iqr = q3 - q1
print(f"IQR: {iqr}")

# Using pandas
df = pd.DataFrame({'values': data})
print(f"\nDescriptive Statistics:\n{df.describe()}")
```

### Percentiles and Quartiles

**Python:**
```python
# Percentiles
p25 = np.percentile(data, 25)
p50 = np.percentile(data, 50)  # Same as median
p75 = np.percentile(data, 75)
p90 = np.percentile(data, 90)

print(f"25th percentile: {p25}")
print(f"50th percentile (median): {p50}")
print(f"75th percentile: {p75}")
print(f"90th percentile: {p90}")

# Quantiles (0 to 1 scale)
q25 = np.quantile(data, 0.25)
q50 = np.quantile(data, 0.50)
q75 = np.quantile(data, 0.75)
```

### Skewness and Kurtosis

**Python:**
```python
from scipy.stats import skew, kurtosis

# Skewness - measure of asymmetry
skewness = skew(data)
print(f"Skewness: {skewness:.2f}")
# Positive: right-skewed, Negative: left-skewed, ~0: symmetric

# Kurtosis - measure of tailedness
kurt = kurtosis(data)
print(f"Kurtosis: {kurt:.2f}")
# Positive: heavy tails, Negative: light tails

# Visual example
import matplotlib.pyplot as plt

# Generate distributions
normal = np.random.normal(0, 1, 1000)
right_skewed = np.random.exponential(2, 1000)
left_skewed = -np.random.exponential(2, 1000)

fig, axes = plt.subplots(1, 3, figsize=(15, 4))

axes[0].hist(normal, bins=30, edgecolor='black')
axes[0].set_title(f'Normal (Skew: {skew(normal):.2f})')

axes[1].hist(right_skewed, bins=30, edgecolor='black')
axes[1].set_title(f'Right Skewed (Skew: {skew(right_skewed):.2f})')

axes[2].hist(left_skewed, bins=30, edgecolor='black')
axes[2].set_title(f'Left Skewed (Skew: {skew(left_skewed):.2f})')

plt.tight_layout()
# plt.show()
```

### Correlation

**Python:**
```python
# Create sample data
np.random.seed(42)
x = np.random.randn(100)
y = 2 * x + np.random.randn(100) * 0.5  # Positive correlation

# Pearson correlation coefficient (linear relationship)
pearson_corr = np.corrcoef(x, y)[0, 1]
print(f"Pearson correlation: {pearson_corr:.3f}")

# Using scipy
from scipy.stats import pearsonr, spearmanr

corr, p_value = pearsonr(x, y)
print(f"Pearson: {corr:.3f}, p-value: {p_value:.4f}")

# Spearman correlation (monotonic relationship)
spearman_corr, p_value = spearmanr(x, y)
print(f"Spearman: {spearman_corr:.3f}, p-value: {p_value:.4f}")

# Correlation matrix for multiple variables
data_matrix = np.random.randn(100, 5)
df = pd.DataFrame(data_matrix, columns=['A', 'B', 'C', 'D', 'E'])

# Correlation matrix
corr_matrix = df.corr()
print(f"\nCorrelation Matrix:\n{corr_matrix}")

# Visualize correlation matrix
import seaborn as sns

plt.figure(figsize=(8, 6))
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', center=0)
plt.title('Correlation Matrix')
# plt.show()
```

---

## Probability Basics

### Basic Probability Concepts

**Python:**
```python
# Probability of an event: P(A) = favorable outcomes / total outcomes

# Example: Rolling a die
total_outcomes = 6
favorable_outcomes = 1  # Getting a 6

prob_six = favorable_outcomes / total_outcomes
print(f"Probability of rolling a 6: {prob_six:.2f}")

# Complement: P(A') = 1 - P(A)
prob_not_six = 1 - prob_six
print(f"Probability of not rolling a 6: {prob_not_six:.2f}")

# Union: P(A ∪ B) = P(A) + P(B) - P(A ∩ B)
# Example: Probability of rolling 5 or 6
prob_five_or_six = 2/6
print(f"Probability of rolling 5 or 6: {prob_five_or_six:.2f}")

# Intersection: P(A ∩ B) for independent events
# P(A ∩ B) = P(A) * P(B)
# Example: Two dice both showing 6
prob_both_six = (1/6) * (1/6)
print(f"Probability of two dice both showing 6: {prob_both_six:.4f}")
```

### Conditional Probability

**Python:**
```python
# Conditional probability: P(A|B) = P(A ∩ B) / P(B)

# Example: Medical test
# P(Disease) = 0.01
# P(Positive|Disease) = 0.95 (sensitivity)
# P(Negative|No Disease) = 0.90 (specificity)

p_disease = 0.01
p_no_disease = 0.99
p_pos_given_disease = 0.95
p_pos_given_no_disease = 0.10  # 1 - specificity

# Bayes' Theorem: P(Disease|Positive) = ?
# P(A|B) = P(B|A) * P(A) / P(B)

p_positive = (p_pos_given_disease * p_disease +
              p_pos_given_no_disease * p_no_disease)

p_disease_given_pos = (p_pos_given_disease * p_disease) / p_positive

print(f"P(Disease|Positive Test): {p_disease_given_pos:.4f}")
# Only ~8.7% even with positive test due to low base rate!
```

### Bayes' Theorem

**Python:**
```python
def bayes_theorem(prior, likelihood, marginal_likelihood):
    """
    Calculate posterior probability using Bayes' Theorem

    P(A|B) = P(B|A) * P(A) / P(B)

    Args:
        prior: P(A) - prior probability
        likelihood: P(B|A) - likelihood
        marginal_likelihood: P(B) - evidence

    Returns:
        posterior: P(A|B)
    """
    posterior = (likelihood * prior) / marginal_likelihood
    return posterior

# Example: Email spam classification
# P(Spam) = 0.3
# P("free"|Spam) = 0.8
# P("free"|Not Spam) = 0.1

p_spam = 0.3
p_not_spam = 0.7
p_free_given_spam = 0.8
p_free_given_not_spam = 0.1

# Calculate P("free")
p_free = (p_free_given_spam * p_spam +
          p_free_given_not_spam * p_not_spam)

# Calculate P(Spam|"free")
p_spam_given_free = bayes_theorem(p_spam, p_free_given_spam, p_free)

print(f"P(Spam|contains 'free'): {p_spam_given_free:.4f}")
```

---

## Probability Distributions

### Discrete Distributions

#### 1. Bernoulli Distribution

**Python:**
```python
from scipy.stats import bernoulli

# Single trial with success probability p
p = 0.3  # Probability of success

# Create distribution
dist = bernoulli(p)

# Probability mass function
print(f"P(X=0): {dist.pmf(0):.2f}")  # Failure
print(f"P(X=1): {dist.pmf(1):.2f}")  # Success

# Generate random samples
samples = dist.rvs(size=1000)
print(f"Sample mean: {samples.mean():.3f} (should be close to {p})")
```

#### 2. Binomial Distribution

**Python:**
```python
from scipy.stats import binom

# n trials, each with probability p
n = 10  # Number of trials
p = 0.3  # Probability of success

# Create distribution
dist = binom(n, p)

# Probability of exactly k successes
for k in range(0, n+1, 2):
    print(f"P(X={k}): {dist.pmf(k):.4f}")

# Cumulative probability
print(f"\nP(X <= 3): {dist.cdf(3):.4f}")
print(f"P(X > 5): {1 - dist.cdf(5):.4f}")

# Expected value and variance
print(f"\nExpected value: {dist.mean():.2f} (n*p = {n*p})")
print(f"Variance: {dist.var():.2f} (n*p*(1-p) = {n*p*(1-p):.2f})")

# Generate samples
samples = dist.rvs(size=1000)

# Visualize
plt.figure(figsize=(10, 5))
plt.hist(samples, bins=range(0, n+2), density=True, alpha=0.7, edgecolor='black')
x = range(0, n+1)
plt.plot(x, dist.pmf(x), 'ro-', ms=8, label='PMF')
plt.xlabel('Number of Successes')
plt.ylabel('Probability')
plt.title(f'Binomial Distribution (n={n}, p={p})')
plt.legend()
# plt.show()
```

#### 3. Poisson Distribution

**Python:**
```python
from scipy.stats import poisson

# Events occurring at rate λ per interval
lambda_rate = 3.5  # Average events per interval

# Create distribution
dist = poisson(lambda_rate)

# Probability of exactly k events
for k in range(0, 10):
    print(f"P(X={k}): {dist.pmf(k):.4f}")

# Example: Website visits per hour
# λ = 50 visits/hour
# What's probability of >60 visits?
visits_dist = poisson(50)
prob_more_than_60 = 1 - visits_dist.cdf(60)
print(f"\nP(visits > 60): {prob_more_than_60:.4f}")

# Visualize
plt.figure(figsize=(10, 5))
x = range(0, 15)
plt.bar(x, dist.pmf(x), alpha=0.7, edgecolor='black')
plt.xlabel('Number of Events')
plt.ylabel('Probability')
plt.title(f'Poisson Distribution (λ={lambda_rate})')
# plt.show()
```

### Continuous Distributions

#### 1. Uniform Distribution

**Python:**
```python
from scipy.stats import uniform

# Uniform distribution over [a, b]
a, b = 0, 10

# Create distribution
dist = uniform(loc=a, scale=b-a)

# Probability density function
x = np.linspace(a, b, 100)
pdf = dist.pdf(x)

# Cumulative distribution function
print(f"P(X <= 5): {dist.cdf(5):.2f}")
print(f"P(3 < X < 7): {dist.cdf(7) - dist.cdf(3):.2f}")

# Generate samples
samples = dist.rvs(size=1000)

# Visualize
fig, axes = plt.subplots(1, 2, figsize=(12, 4))

axes[0].plot(x, pdf, 'b-', lw=2)
axes[0].set_title('Probability Density Function')
axes[0].set_xlabel('x')
axes[0].set_ylabel('Density')

axes[1].hist(samples, bins=30, density=True, alpha=0.7, edgecolor='black')
axes[1].set_title('Histogram of Samples')
axes[1].set_xlabel('x')
axes[1].set_ylabel('Density')

plt.tight_layout()
# plt.show()
```

#### 2. Normal (Gaussian) Distribution

**Python:**
```python
from scipy.stats import norm

# Normal distribution N(μ, σ²)
mu = 0  # Mean
sigma = 1  # Standard deviation

# Create distribution
dist = norm(loc=mu, scale=sigma)

# Probability density function
x = np.linspace(-4, 4, 1000)
pdf = dist.pdf(x)

# Cumulative distribution function
print(f"P(X <= 0): {dist.cdf(0):.4f}")
print(f"P(-1 < X < 1): {dist.cdf(1) - dist.cdf(-1):.4f}")
print(f"P(-2 < X < 2): {dist.cdf(2) - dist.cdf(-2):.4f}")
print(f"P(-3 < X < 3): {dist.cdf(3) - dist.cdf(-3):.4f}")

# Empirical Rule (68-95-99.7)
# 68% within 1σ, 95% within 2σ, 99.7% within 3σ

# Z-scores (standardization)
value = 1.5
z_score = (value - mu) / sigma
print(f"\nZ-score for {value}: {z_score:.2f}")

# Inverse: Find value for given probability
p = 0.95
value_at_p = dist.ppf(p)
print(f"Value at 95th percentile: {value_at_p:.2f}")

# Generate samples
samples = dist.rvs(size=10000)

# Visualize
plt.figure(figsize=(12, 5))

plt.subplot(1, 2, 1)
plt.plot(x, pdf, 'b-', lw=2, label='PDF')
plt.fill_between(x, 0, pdf, alpha=0.3)
plt.xlabel('x')
plt.ylabel('Density')
plt.title(f'Normal Distribution (μ={mu}, σ={sigma})')
plt.legend()

plt.subplot(1, 2, 2)
plt.hist(samples, bins=50, density=True, alpha=0.7, edgecolor='black', label='Samples')
plt.plot(x, pdf, 'r-', lw=2, label='Theoretical PDF')
plt.xlabel('x')
plt.ylabel('Density')
plt.title('Histogram vs Theoretical Distribution')
plt.legend()

plt.tight_layout()
# plt.show()
```

#### 3. Exponential Distribution

**Python:**
```python
from scipy.stats import expon

# Exponential distribution (waiting times)
lambda_rate = 0.5  # Rate parameter

# Create distribution
dist = expon(scale=1/lambda_rate)

# PDF and CDF
x = np.linspace(0, 10, 1000)
pdf = dist.pdf(x)
cdf = dist.cdf(x)

# Example: Time between events
# Average time = 1/λ
print(f"Mean time between events: {dist.mean():.2f}")

# Probability of event within time t
t = 2
prob = dist.cdf(t)
print(f"P(time <= {t}): {prob:.4f}")

# Memoryless property: P(X > s+t | X > s) = P(X > t)
s, t = 1, 2
prob1 = 1 - dist.cdf(s + t)
prob2 = 1 - dist.cdf(s)
conditional = prob1 / prob2
prob_t = 1 - dist.cdf(t)
print(f"\nMemoryless property check:")
print(f"P(X > {s+t} | X > {s}): {conditional:.4f}")
print(f"P(X > {t}): {prob_t:.4f}")

# Visualize
fig, axes = plt.subplots(1, 2, figsize=(12, 4))

axes[0].plot(x, pdf, 'b-', lw=2)
axes[0].set_title('Exponential PDF')
axes[0].set_xlabel('Time')
axes[0].set_ylabel('Density')

axes[1].plot(x, cdf, 'r-', lw=2)
axes[1].set_title('Exponential CDF')
axes[1].set_xlabel('Time')
axes[1].set_ylabel('Cumulative Probability')

plt.tight_layout()
# plt.show()
```

---

## Statistical Inference

### Central Limit Theorem

**Python:**
```python
# Central Limit Theorem: Sample means approach normal distribution
# even if population is not normal

# Non-normal population (exponential)
population = np.random.exponential(scale=2, size=100000)

sample_sizes = [5, 10, 30, 100]
sample_means = {n: [] for n in sample_sizes}

# Take many samples and compute means
num_samples = 1000

for n in sample_sizes:
    for _ in range(num_samples):
        sample = np.random.choice(population, size=n, replace=True)
        sample_means[n].append(sample.mean())

# Visualize
fig, axes = plt.subplots(2, 3, figsize=(15, 8))

# Original population
axes[0, 0].hist(population, bins=50, density=True, alpha=0.7, edgecolor='black')
axes[0, 0].set_title('Original Population (Exponential)')
axes[0, 0].set_xlabel('Value')
axes[0, 0].set_ylabel('Density')

# Sample means distributions
positions = [(0, 1), (0, 2), (1, 0), (1, 1)]

for idx, n in enumerate(sample_sizes):
    row, col = positions[idx]
    axes[row, col].hist(sample_means[n], bins=30, density=True,
                        alpha=0.7, edgecolor='black')

    # Overlay normal distribution
    mu = np.mean(sample_means[n])
    sigma = np.std(sample_means[n])
    x = np.linspace(mu - 3*sigma, mu + 3*sigma, 100)
    axes[row, col].plot(x, norm.pdf(x, mu, sigma), 'r-', lw=2)

    axes[row, col].set_title(f'Sample Means (n={n})')
    axes[row, col].set_xlabel('Sample Mean')
    axes[row, col].set_ylabel('Density')

axes[1, 2].axis('off')  # Hide last subplot

plt.tight_layout()
# plt.show()

print("As sample size increases, distribution of sample means")
print("becomes more normal, regardless of population distribution!")
```

### Confidence Intervals

**Python:**
```python
from scipy import stats

# Confidence interval for mean
data = np.random.normal(100, 15, size=100)

# Calculate confidence interval
confidence_level = 0.95
mean = np.mean(data)
std_err = stats.sem(data)  # Standard error of mean
margin_of_error = std_err * stats.t.ppf((1 + confidence_level) / 2, len(data) - 1)

ci_lower = mean - margin_of_error
ci_upper = mean + margin_of_error

print(f"Sample mean: {mean:.2f}")
print(f"95% Confidence Interval: [{ci_lower:.2f}, {ci_upper:.2f}]")
print(f"Interpretation: We are 95% confident the true mean lies in this interval")

# Using scipy directly
ci = stats.t.interval(confidence_level, len(data)-1, loc=mean, scale=std_err)
print(f"CI using scipy: [{ci[0]:.2f}, {ci[1]:.2f}]")

# For proportions
successes = 60
trials = 100
proportion = successes / trials

# Wilson score interval (better for proportions)
from statsmodels.stats.proportion import proportion_confint

ci_lower, ci_upper = proportion_confint(successes, trials, alpha=1-confidence_level, method='wilson')
print(f"\nProportion: {proportion:.2f}")
print(f"95% CI for proportion: [{ci_lower:.3f}, {ci_upper:.3f}]")
```

---

## Hypothesis Testing

### T-Test

**Python:**
```python
from scipy.stats import ttest_1samp, ttest_ind, ttest_rel

# One-sample t-test
# H0: μ = μ0 vs H1: μ ≠ μ0
data = np.random.normal(105, 15, size=100)
hypothesized_mean = 100

t_stat, p_value = ttest_1samp(data, hypothesized_mean)

print("One-Sample T-Test")
print(f"t-statistic: {t_stat:.4f}")
print(f"p-value: {p_value:.4f}")

alpha = 0.05
if p_value < alpha:
    print(f"Reject H0: Mean is significantly different from {hypothesized_mean}")
else:
    print(f"Fail to reject H0: No significant difference from {hypothesized_mean}")

# Two-sample t-test (independent)
# H0: μ1 = μ2 vs H1: μ1 ≠ μ2
group1 = np.random.normal(100, 15, size=100)
group2 = np.random.normal(105, 15, size=100)

t_stat, p_value = ttest_ind(group1, group2)

print("\nTwo-Sample T-Test (Independent)")
print(f"t-statistic: {t_stat:.4f}")
print(f"p-value: {p_value:.4f}")

if p_value < alpha:
    print("Reject H0: Means are significantly different")
else:
    print("Fail to reject H0: No significant difference between means")

# Paired t-test
# H0: μ_diff = 0 vs H1: μ_diff ≠ 0
before = np.random.normal(100, 15, size=50)
after = before + np.random.normal(5, 10, size=50)  # Treatment effect

t_stat, p_value = ttest_rel(before, after)

print("\nPaired T-Test")
print(f"t-statistic: {t_stat:.4f}")
print(f"p-value: {p_value:.4f}")

if p_value < alpha:
    print("Reject H0: Significant difference between before and after")
else:
    print("Fail to reject H0: No significant difference")
```

### Chi-Square Test

**Python:**
```python
from scipy.stats import chi2_contingency, chisquare

# Chi-square goodness of fit
# H0: Data follows expected distribution
observed = [45, 55, 30, 20]  # Observed frequencies
expected = [40, 40, 40, 40]  # Expected frequencies

chi2_stat, p_value = chisquare(observed, expected)

print("Chi-Square Goodness of Fit")
print(f"Chi-square statistic: {chi2_stat:.4f}")
print(f"p-value: {p_value:.4f}")

# Chi-square test of independence
# H0: Variables are independent
# Example: Gender vs Product Preference
contingency_table = np.array([
    [30, 10, 20],  # Male preferences
    [15, 25, 30]   # Female preferences
])

chi2_stat, p_value, dof, expected_freq = chi2_contingency(contingency_table)

print("\nChi-Square Test of Independence")
print(f"Chi-square statistic: {chi2_stat:.4f}")
print(f"p-value: {p_value:.4f}")
print(f"Degrees of freedom: {dof}")
print(f"Expected frequencies:\n{expected_freq}")

if p_value < alpha:
    print("Reject H0: Variables are dependent")
else:
    print("Fail to reject H0: Variables are independent")
```

### ANOVA

**Python:**
```python
from scipy.stats import f_oneway

# One-way ANOVA
# H0: μ1 = μ2 = μ3 vs H1: At least one mean is different
group1 = np.random.normal(100, 15, size=50)
group2 = np.random.normal(105, 15, size=50)
group3 = np.random.normal(110, 15, size=50)

f_stat, p_value = f_oneway(group1, group2, group3)

print("One-Way ANOVA")
print(f"F-statistic: {f_stat:.4f}")
print(f"p-value: {p_value:.4f}")

if p_value < alpha:
    print("Reject H0: At least one group mean is significantly different")
else:
    print("Fail to reject H0: No significant difference between group means")
```

---

## Practical Applications

### A/B Testing

**Python:**
```python
# A/B Test Example: Website conversion rates

# Control group (A)
control_visitors = 1000
control_conversions = 50
control_rate = control_conversions / control_visitors

# Treatment group (B)
treatment_visitors = 1000
treatment_conversions = 65
treatment_rate = treatment_conversions / treatment_visitors

print(f"Control conversion rate: {control_rate:.3%}")
print(f"Treatment conversion rate: {treatment_rate:.3%}")
print(f"Relative improvement: {(treatment_rate/control_rate - 1):.1%}")

# Statistical test
from statsmodels.stats.proportion import proportions_ztest

counts = np.array([control_conversions, treatment_conversions])
nobs = np.array([control_visitors, treatment_visitors])

z_stat, p_value = proportions_ztest(counts, nobs)

print(f"\nZ-statistic: {z_stat:.4f}")
print(f"P-value: {p_value:.4f}")

if p_value < 0.05:
    print("Result is statistically significant!")
else:
    print("Result is NOT statistically significant")

# Calculate required sample size for desired power
from statsmodels.stats.power import zt_ind_solve_power

effect_size = (treatment_rate - control_rate) / np.sqrt(control_rate * (1 - control_rate))
required_n = zt_ind_solve_power(effect_size=effect_size, alpha=0.05, power=0.8)
print(f"\nRequired sample size per group: {required_n:.0f}")
```

---

## Key Takeaways

1. **Descriptive statistics** summarize data: mean, median, std, correlation
2. **Probability distributions** model uncertainty: normal, binomial, Poisson
3. **Central Limit Theorem** enables inference from samples
4. **Confidence intervals** quantify uncertainty in estimates
5. **Hypothesis tests** make data-driven decisions
6. **P-values** measure evidence against null hypothesis
7. **Statistical significance ≠ practical significance**

---

## Next Chapter

Continue to [Linear Algebra](./02-Linear-Algebra.md) to learn the mathematical foundations for machine learning.

[← Back to Foundations](../01-Foundations/) | [Next: Linear Algebra →](./02-Linear-Algebra.md)
