---
title: scikit-learn
permalink: /sklearn/
---

# scikit-learn

Notes on preprocessing, training, and evaluating machine learning models.

## Topics to explore

- Transformers, estimators, and the `fit` / `predict` interface.
- Pipelines and column-wise preprocessing.
- Cross-validation and hyperparameter search.
- Metrics for classification, regression, and imbalanced datasets.

## Example: a preprocessing pipeline

```python
from sklearn.linear_model import LogisticRegression
from sklearn.pipeline import make_pipeline
from sklearn.preprocessing import StandardScaler

model = make_pipeline(StandardScaler(), LogisticRegression(max_iter=1000))
model.fit(X_train, y_train)
predictions = model.predict(X_test)
```

This snippet assumes an existing numeric train/test split. Keeping preprocessing
inside the pipeline prevents fitting the scaler on the test set.

[Back to home]({{ '/' | relative_url }})
