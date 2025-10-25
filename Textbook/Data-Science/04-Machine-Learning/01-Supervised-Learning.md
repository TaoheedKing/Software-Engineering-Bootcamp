# Chapter 1: Supervised Learning

## Table of Contents
- [Introduction](#introduction)
- [Linear Regression](#linear-regression)
- [Logistic Regression](#logistic-regression)
- [Decision Trees](#decision-trees)
- [Support Vector Machines](#support-vector-machines)
- [K-Nearest Neighbors](#k-nearest-neighbors)
- [Model Evaluation](#model-evaluation)

---

## Introduction

### What is Supervised Learning?

Supervised learning is a type of machine learning where the model learns from labeled data - data where the correct answer (target variable) is known.

**Types:**
- **Regression**: Predict continuous values (e.g., house prices, temperature)
- **Classification**: Predict discrete classes (e.g., spam/not spam, cat/dog)

**Key Concepts:**
- **Features (X)**: Input variables used for prediction
- **Target (y)**: Output variable we want to predict
- **Training**: Learning patterns from data
- **Testing**: Evaluating model on unseen data

---

## Linear Regression

### Simple Linear Regression

Predicting a target variable using a single feature.

**Python:**
```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, r2_score

# Generate sample data
np.random.seed(42)
X = np.random.rand(100, 1) * 10  # Feature
y = 2.5 * X.squeeze() + 5 + np.random.randn(100) * 2  # Target with noise

# Visualize data
plt.figure(figsize=(10, 6))
plt.scatter(X, y, alpha=0.6)
plt.xlabel('X')
plt.ylabel('y')
plt.title('Sample Data for Linear Regression')
# plt.show()

# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Create and train model
model = LinearRegression()
model.fit(X_train, y_train)

# Model parameters
print(f"Coefficient (slope): {model.coef_[0]:.2f}")
print(f"Intercept: {model.intercept_:.2f}")

# Make predictions
y_pred = model.predict(X_test)

# Evaluate
mse = mean_squared_error(y_test, y_pred)
rmse = np.sqrt(mse)
r2 = r2_score(y_test, y_pred)

print(f"\nModel Performance:")
print(f"RMSE: {rmse:.2f}")
print(f"R² Score: {r2:.4f}")

# Visualize results
plt.figure(figsize=(12, 5))

plt.subplot(1, 2, 1)
plt.scatter(X_train, y_train, alpha=0.6, label='Training data')
plt.scatter(X_test, y_test, alpha=0.6, label='Test data')
plt.plot(X, model.predict(X), 'r-', linewidth=2, label='Regression line')
plt.xlabel('X')
plt.ylabel('y')
plt.title('Linear Regression Fit')
plt.legend()

plt.subplot(1, 2, 2)
plt.scatter(y_test, y_pred, alpha=0.6)
plt.plot([y_test.min(), y_test.max()],
         [y_test.min(), y_test.max()],
         'r--', lw=2)
plt.xlabel('Actual Values')
plt.ylabel('Predicted Values')
plt.title('Actual vs Predicted')

plt.tight_layout()
# plt.show()
```

### Multiple Linear Regression

**Python:**
```python
from sklearn.datasets import fetch_california_housing
from sklearn.preprocessing import StandardScaler

# Load dataset
housing = fetch_california_housing()
X = pd.DataFrame(housing.data, columns=housing.feature_names)
y = housing.target

print("Dataset shape:", X.shape)
print("\nFeatures:")
print(X.head())

# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Standardize features
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

# Train model
model = LinearRegression()
model.fit(X_train_scaled, y_train)

# Predictions
y_pred = model.predict(X_test_scaled)

# Evaluate
mse = mean_squared_error(y_test, y_pred)
rmse = np.sqrt(mse)
r2 = r2_score(y_test, y_pred)

print(f"\nModel Performance:")
print(f"RMSE: {rmse:.4f}")
print(f"R² Score: {r2:.4f}")

# Feature importance
feature_importance = pd.DataFrame({
    'Feature': X.columns,
    'Coefficient': model.coef_
}).sort_values('Coefficient', key=abs, ascending=False)

print("\nFeature Importance (by coefficient magnitude):")
print(feature_importance)

# Visualize coefficients
plt.figure(figsize=(10, 6))
plt.barh(feature_importance['Feature'], feature_importance['Coefficient'])
plt.xlabel('Coefficient Value')
plt.title('Feature Coefficients')
plt.tight_layout()
# plt.show()
```

### Polynomial Regression

**Python:**
```python
from sklearn.preprocessing import PolynomialFeatures
from sklearn.pipeline import Pipeline

# Generate non-linear data
np.random.seed(42)
X = np.random.rand(100, 1) * 10
y = 0.5 * X.squeeze()**2 - 3 * X.squeeze() + 5 + np.random.randn(100) * 5

# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Compare different polynomial degrees
degrees = [1, 2, 3, 5]
colors = ['blue', 'green', 'red', 'purple']

plt.figure(figsize=(12, 8))

for idx, (degree, color) in enumerate(zip(degrees, colors)):
    # Create polynomial features and fit model
    poly_model = Pipeline([
        ('poly_features', PolynomialFeatures(degree=degree)),
        ('linear_regression', LinearRegression())
    ])

    poly_model.fit(X_train, y_train)
    y_pred = poly_model.predict(X_test)

    # Calculate metrics
    rmse = np.sqrt(mean_squared_error(y_test, y_pred))
    r2 = r2_score(y_test, y_pred)

    # Plot
    plt.subplot(2, 2, idx + 1)
    plt.scatter(X_train, y_train, alpha=0.6, label='Training data')
    plt.scatter(X_test, y_test, alpha=0.6, label='Test data')

    X_plot = np.linspace(X.min(), X.max(), 100).reshape(-1, 1)
    y_plot = poly_model.predict(X_plot)
    plt.plot(X_plot, y_plot, color=color, linewidth=2,
             label=f'Degree {degree}')

    plt.xlabel('X')
    plt.ylabel('y')
    plt.title(f'Degree {degree}: RMSE={rmse:.2f}, R²={r2:.3f}')
    plt.legend()

plt.tight_layout()
# plt.show()
```

### Regularization (Ridge, Lasso)

**Python:**
```python
from sklearn.linear_model import Ridge, Lasso, ElasticNet

# Generate data with many features
np.random.seed(42)
n_samples, n_features = 100, 50
X = np.random.randn(n_samples, n_features)

# Only first 10 features are relevant
true_coef = np.zeros(n_features)
true_coef[:10] = np.random.randn(10)
y = X @ true_coef + np.random.randn(n_samples) * 0.5

# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Compare models
models = {
    'Linear Regression': LinearRegression(),
    'Ridge (L2)': Ridge(alpha=1.0),
    'Lasso (L1)': Lasso(alpha=0.1),
    'ElasticNet': ElasticNet(alpha=0.1, l1_ratio=0.5)
}

results = []

for name, model in models.items():
    model.fit(X_train, y_train)
    y_pred = model.predict(X_test)

    rmse = np.sqrt(mean_squared_error(y_test, y_pred))
    r2 = r2_score(y_test, y_pred)
    non_zero = np.sum(np.abs(model.coef_) > 1e-5)

    results.append({
        'Model': name,
        'RMSE': rmse,
        'R²': r2,
        'Non-zero coefficients': non_zero
    })

results_df = pd.DataFrame(results)
print(results_df)

# Visualize coefficients
fig, axes = plt.subplots(2, 2, figsize=(14, 10))

for idx, (name, model) in enumerate(models.items()):
    ax = axes[idx // 2, idx % 2]
    ax.stem(range(n_features), model.coef_, basefmt=' ')
    ax.axhline(y=0, color='k', linestyle='--', alpha=0.3)
    ax.set_title(f'{name} Coefficients')
    ax.set_xlabel('Feature Index')
    ax.set_ylabel('Coefficient Value')

plt.tight_layout()
# plt.show()
```

---

## Logistic Regression

### Binary Classification

**Python:**
```python
from sklearn.linear_model import LogisticRegression
from sklearn.datasets import make_classification
from sklearn.metrics import (accuracy_score, precision_score,
                             recall_score, f1_score, confusion_matrix,
                             classification_report, roc_curve, roc_auc_score)

# Generate binary classification data
X, y = make_classification(
    n_samples=1000,
    n_features=20,
    n_informative=15,
    n_redundant=5,
    random_state=42
)

# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Train model
model = LogisticRegression(random_state=42)
model.fit(X_train, y_train)

# Predictions
y_pred = model.predict(X_test)
y_pred_proba = model.predict_proba(X_test)[:, 1]

# Evaluate
accuracy = accuracy_score(y_test, y_pred)
precision = precision_score(y_test, y_pred)
recall = recall_score(y_test, y_pred)
f1 = f1_score(y_test, y_pred)
auc = roc_auc_score(y_test, y_pred_proba)

print("Model Performance:")
print(f"Accuracy: {accuracy:.4f}")
print(f"Precision: {precision:.4f}")
print(f"Recall: {recall:.4f}")
print(f"F1 Score: {f1:.4f}")
print(f"AUC-ROC: {auc:.4f}")

print("\nClassification Report:")
print(classification_report(y_test, y_pred))

# Confusion Matrix
cm = confusion_matrix(y_test, y_pred)
print("\nConfusion Matrix:")
print(cm)

# Visualize results
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# Confusion Matrix
import seaborn as sns
sns.heatmap(cm, annot=True, fmt='d', cmap='Blues', ax=axes[0])
axes[0].set_title('Confusion Matrix')
axes[0].set_xlabel('Predicted')
axes[0].set_ylabel('Actual')

# ROC Curve
fpr, tpr, thresholds = roc_curve(y_test, y_pred_proba)
axes[1].plot(fpr, tpr, linewidth=2, label=f'ROC (AUC = {auc:.3f})')
axes[1].plot([0, 1], [0, 1], 'k--', label='Random')
axes[1].set_xlabel('False Positive Rate')
axes[1].set_ylabel('True Positive Rate')
axes[1].set_title('ROC Curve')
axes[1].legend()
axes[1].grid(True, alpha=0.3)

plt.tight_layout()
# plt.show()
```

### Multi-class Classification

**Python:**
```python
from sklearn.datasets import load_iris
from sklearn.multiclass import OneVsRestClassifier

# Load iris dataset (3 classes)
iris = load_iris()
X, y = iris.data, iris.target

# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

# Train model (automatically handles multi-class)
model = LogisticRegression(multi_class='multinomial', max_iter=1000)
model.fit(X_train, y_train)

# Predictions
y_pred = model.predict(X_test)
y_pred_proba = model.predict_proba(X_test)

# Evaluate
accuracy = accuracy_score(y_test, y_pred)
print(f"Accuracy: {accuracy:.4f}")

print("\nClassification Report:")
print(classification_report(y_test, y_pred, target_names=iris.target_names))

# Confusion Matrix
cm = confusion_matrix(y_test, y_pred)
plt.figure(figsize=(8, 6))
sns.heatmap(cm, annot=True, fmt='d', cmap='Blues',
            xticklabels=iris.target_names,
            yticklabels=iris.target_names)
plt.title('Multi-class Confusion Matrix')
plt.xlabel('Predicted')
plt.ylabel('Actual')
# plt.show()
```

---

## Decision Trees

**Python:**
```python
from sklearn.tree import DecisionTreeClassifier, DecisionTreeRegressor
from sklearn.tree import plot_tree

# Classification example
X, y = make_classification(
    n_samples=500,
    n_features=10,
    n_informative=8,
    n_redundant=2,
    random_state=42
)

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Train decision tree
dt_model = DecisionTreeClassifier(
    max_depth=5,
    min_samples_split=20,
    min_samples_leaf=10,
    random_state=42
)

dt_model.fit(X_train, y_train)

# Predictions
y_pred = dt_model.predict(X_test)

# Evaluate
accuracy = accuracy_score(y_test, y_pred)
print(f"Decision Tree Accuracy: {accuracy:.4f}")

# Feature importance
feature_importance = pd.DataFrame({
    'Feature': [f'Feature_{i}' for i in range(X.shape[1])],
    'Importance': dt_model.feature_importances_
}).sort_values('Importance', ascending=False)

print("\nFeature Importance:")
print(feature_importance)

# Visualize tree
plt.figure(figsize=(20, 10))
plot_tree(dt_model, filled=True, feature_names=[f'X{i}' for i in range(X.shape[1])],
          class_names=['Class 0', 'Class 1'], fontsize=10)
plt.title('Decision Tree Visualization')
# plt.show()

# Visualize feature importance
plt.figure(figsize=(10, 6))
plt.barh(feature_importance['Feature'], feature_importance['Importance'])
plt.xlabel('Importance')
plt.title('Feature Importance in Decision Tree')
plt.tight_layout()
# plt.show()
```

---

## Support Vector Machines

**Python:**
```python
from sklearn.svm import SVC, SVR

# Generate non-linearly separable data
from sklearn.datasets import make_moons

X, y = make_moons(n_samples=500, noise=0.2, random_state=42)

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Compare different kernels
kernels = ['linear', 'rbf', 'poly']
fig, axes = plt.subplots(1, 3, figsize=(18, 5))

for idx, kernel in enumerate(kernels):
    # Train SVM
    svm_model = SVC(kernel=kernel, gamma='scale')
    svm_model.fit(X_train, y_train)

    # Predictions
    y_pred = svm_model.predict(X_test)
    accuracy = accuracy_score(y_test, y_pred)

    # Visualize decision boundary
    h = 0.02  # Step size in mesh
    x_min, x_max = X[:, 0].min() - 0.5, X[:, 0].max() + 0.5
    y_min, y_max = X[:, 1].min() - 0.5, X[:, 1].max() + 0.5
    xx, yy = np.meshgrid(np.arange(x_min, x_max, h),
                         np.arange(y_min, y_max, h))

    Z = svm_model.predict(np.c_[xx.ravel(), yy.ravel()])
    Z = Z.reshape(xx.shape)

    axes[idx].contourf(xx, yy, Z, alpha=0.4, cmap='RdYlBu')
    axes[idx].scatter(X_train[:, 0], X_train[:, 1], c=y_train,
                     cmap='RdYlBu', edgecolors='black', alpha=0.6)
    axes[idx].set_title(f'{kernel.upper()} Kernel (Acc: {accuracy:.3f})')
    axes[idx].set_xlabel('Feature 1')
    axes[idx].set_ylabel('Feature 2')

plt.tight_layout()
# plt.show()
```

---

## K-Nearest Neighbors

**Python:**
```python
from sklearn.neighbors import KNeighborsClassifier, KNeighborsRegressor

# Generate data
X, y = make_classification(
    n_samples=500,
    n_features=2,
    n_informative=2,
    n_redundant=0,
    n_clusters_per_class=1,
    random_state=42
)

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Compare different k values
k_values = [1, 5, 15, 50]
fig, axes = plt.subplots(2, 2, figsize=(14, 12))

for idx, k in enumerate(k_values):
    # Train KNN
    knn_model = KNeighborsClassifier(n_neighbors=k)
    knn_model.fit(X_train, y_train)

    # Predictions
    y_pred = knn_model.predict(X_test)
    accuracy = accuracy_score(y_test, y_pred)

    # Visualize decision boundary
    h = 0.02
    x_min, x_max = X[:, 0].min() - 1, X[:, 0].max() + 1
    y_min, y_max = X[:, 1].min() - 1, X[:, 1].max() + 1
    xx, yy = np.meshgrid(np.arange(x_min, x_max, h),
                         np.arange(y_min, y_max, h))

    Z = knn_model.predict(np.c_[xx.ravel(), yy.ravel()])
    Z = Z.reshape(xx.shape)

    ax = axes[idx // 2, idx % 2]
    ax.contourf(xx, yy, Z, alpha=0.4, cmap='RdYlBu')
    ax.scatter(X_train[:, 0], X_train[:, 1], c=y_train,
              cmap='RdYlBu', edgecolors='black', alpha=0.6)
    ax.set_title(f'KNN (k={k}), Accuracy: {accuracy:.3f}')
    ax.set_xlabel('Feature 1')
    ax.set_ylabel('Feature 2')

plt.tight_layout()
# plt.show()

# Find optimal k
k_range = range(1, 31)
train_scores = []
test_scores = []

for k in k_range:
    knn = KNeighborsClassifier(n_neighbors=k)
    knn.fit(X_train, y_train)

    train_scores.append(knn.score(X_train, y_train))
    test_scores.append(knn.score(X_test, y_test))

# Plot accuracy vs k
plt.figure(figsize=(10, 6))
plt.plot(k_range, train_scores, label='Training Accuracy', marker='o')
plt.plot(k_range, test_scores, label='Test Accuracy', marker='s')
plt.xlabel('k (Number of Neighbors)')
plt.ylabel('Accuracy')
plt.title('KNN: Accuracy vs k')
plt.legend()
plt.grid(True, alpha=0.3)
# plt.show()

optimal_k = k_range[np.argmax(test_scores)]
print(f"Optimal k: {optimal_k}")
```

---

## Model Evaluation

### Cross-Validation

**Python:**
```python
from sklearn.model_selection import cross_val_score, cross_validate

# Load data
X, y = make_classification(
    n_samples=1000,
    n_features=20,
    random_state=42
)

# Model
model = LogisticRegression()

# Simple cross-validation
cv_scores = cross_val_score(model, X, y, cv=5, scoring='accuracy')

print("Cross-Validation Scores:")
print(cv_scores)
print(f"\nMean CV Score: {cv_scores.mean():.4f} (+/- {cv_scores.std() * 2:.4f})")

# Multiple metrics
scoring = ['accuracy', 'precision', 'recall', 'f1', 'roc_auc']
cv_results = cross_validate(model, X, y, cv=5, scoring=scoring)

print("\nDetailed CV Results:")
for metric in scoring:
    scores = cv_results[f'test_{metric}']
    print(f"{metric.capitalize()}: {scores.mean():.4f} (+/- {scores.std() * 2:.4f})")
```

### Hyperparameter Tuning

**Python:**
```python
from sklearn.model_selection import GridSearchCV, RandomizedSearchCV
from scipy.stats import randint, uniform

# Define parameter grid
param_grid = {
    'C': [0.1, 1, 10, 100],
    'penalty': ['l1', 'l2'],
    'solver': ['liblinear']
}

# Grid Search
grid_search = GridSearchCV(
    LogisticRegression(),
    param_grid,
    cv=5,
    scoring='accuracy',
    n_jobs=-1,
    verbose=1
)

grid_search.fit(X_train, y_train)

print("Best Parameters:", grid_search.best_params_)
print("Best CV Score:", grid_search.best_score_)

# Test on held-out set
best_model = grid_search.best_estimator_
test_accuracy = best_model.score(X_test, y_test)
print(f"Test Accuracy: {test_accuracy:.4f}")

# Randomized Search (faster for large parameter spaces)
param_distributions = {
    'C': uniform(0.1, 100),
    'penalty': ['l1', 'l2'],
    'solver': ['liblinear']
}

random_search = RandomizedSearchCV(
    LogisticRegression(),
    param_distributions,
    n_iter=20,
    cv=5,
    scoring='accuracy',
    n_jobs=-1,
    random_state=42,
    verbose=1
)

random_search.fit(X_train, y_train)

print("\nRandomized Search Best Parameters:", random_search.best_params_)
print("Randomized Search Best CV Score:", random_search.best_score_)
```

### Learning Curves

**Python:**
```python
from sklearn.model_selection import learning_curve

# Generate learning curves
train_sizes, train_scores, val_scores = learning_curve(
    LogisticRegression(),
    X, y,
    cv=5,
    train_sizes=np.linspace(0.1, 1.0, 10),
    scoring='accuracy',
    n_jobs=-1
)

# Calculate mean and std
train_mean = train_scores.mean(axis=1)
train_std = train_scores.std(axis=1)
val_mean = val_scores.mean(axis=1)
val_std = val_scores.std(axis=1)

# Plot
plt.figure(figsize=(10, 6))
plt.plot(train_sizes, train_mean, label='Training score', marker='o')
plt.fill_between(train_sizes, train_mean - train_std, train_mean + train_std, alpha=0.15)

plt.plot(train_sizes, val_mean, label='Cross-validation score', marker='s')
plt.fill_between(train_sizes, val_mean - val_std, val_mean + val_std, alpha=0.15)

plt.xlabel('Training Set Size')
plt.ylabel('Accuracy')
plt.title('Learning Curves')
plt.legend()
plt.grid(True, alpha=0.3)
# plt.show()
```

---

## Key Takeaways

1. **Linear Regression**: Best for linear relationships, interpretable
2. **Logistic Regression**: Simple yet effective for classification
3. **Decision Trees**: Non-linear, interpretable, prone to overfitting
4. **SVM**: Powerful for high-dimensional data, kernel trick for non-linearity
5. **KNN**: Simple, non-parametric, sensitive to scale and k value
6. **Regularization** (Ridge/Lasso) prevents overfitting
7. **Cross-validation** provides robust performance estimates
8. **Hyperparameter tuning** critical for optimal performance

---

## Next Chapter

Continue to [Unsupervised Learning](./02-Unsupervised-Learning.md) to learn about clustering and dimensionality reduction.

[← Back to Machine Learning](../04-Machine-Learning/) | [Next: Unsupervised Learning →](./02-Unsupervised-Learning.md)
