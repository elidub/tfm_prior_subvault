---
title: "When Data Is Scarce: The Strength of the Prior in Tabular Foundation Models"
authors: Florian D. van Leeuwen, Sara van Erp
year: 2026
elias_mainvault: "[[leeuwenWhenDataScarce2026]]"
---

- Interesting how they
	- First do a comparison with an analytical baseline
	- Also include calibration (Brier for binary classification and Coverage for regression)

Motivated from sample size limitations in fields such as medicine and economics. A non tfm approach to a small $n$ is typically fitting simple algorithms (which are prone to overfitting) or by encoding knowledge through a bayesian framework by placing reasonble priors on model parameters.

### Methodology
Evalute PFNs in two settings:
- synthetic experiments comparing PFNs to correctly specified models with and without informative priors
- subsampled datasets from the TabArena benchmark. (might be useful here to check how they randomly subsampled, although i imagine subsampling rows and columns is different)
	- exclude datasets with more than 100 predictors to avoid high dimensional sub samples ($p>n$)
	- exclude datasets with missing values (not all models handle these natively)
	- 
Models evaluated: TabPFN v2.5, TabICLv2, Real-TabPFN v2.5 and TabDPT v1.1
Baseline models:  linear/logistic regressions.

By correctly specified models its implied that there exists no model misspecification error. the datasets generated are synthetic therefore the true inputs are known. to isolate parametric estimation to be the only source of error, baseline models are fed the true structure (i.e. if the true underlying relationship requires an interaction feature between X1 and X2, the baseline models are revealed to this interaction)

They also make use of Brier which is a metric that penalizes over and underconfidence for classification. Essentially, if the model predicts the positive class with 60% probability, then (0.60-1)^2 is your score. the lower the brier score the better. 0 is a perfect score.

### Results
- for binary classification, PFNs are on par or slightly better than the correctly specified model.
- PFNs perform worse on regression tasks than baseline. Only in the most difficult scenario (ME + I + NL + NI) with 100 samples did PFNs come within 1% of baseline model performance.