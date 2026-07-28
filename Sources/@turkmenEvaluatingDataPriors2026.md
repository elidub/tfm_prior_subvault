---
title: Towards Evaluating Data Priors for Tabular Foundation Models
authors: Zeynep Türkmen, Kürşat Kaya, Alexander Pfefferle, Frank Hutter
year: 2026
elias_mainvault: "[[turkmenEvaluatingDataPriors2026]]"
---
### Important quotes

> Future work could examine whether prior-specific hyperparameter tuning changes the relative performance of different priors.’ 

Rather than tweaking prior specific hyperparameters, the study compares different priors under a controlled and consistent setup.

Has the very setup we would like to do: 
A unified interface that holds every component in the full pipeline constant except for the prior choice. Priors from different models are pulled and run through the entire pipeline. The priors at the data level are compared through a set of dataset statistics. Further evaluated downstream after training a nanoTabPFN.

### Evaluation
final evaluation performed on fixed subset of TabArena v0.1 classification tasks. used OpenMLs predefined train/test splits and evaluated the model across three folds.
(curious however, how did they decide which datasets to use? could it be that they only chose to display those datasets which showed desired results?)

## Conclusion

- ***Data level similarity computed through summary statistics and downstream predictive performance do not track reliably*.** 
When tweaking levers within a prior, do not make any inferences on downstream performance simply because of shared structural statistics between the generated graphs. This is a pitfall as per this paper!

- ***no single prior was uniformly best under all criteria***. 
some priors achieved stronger average ROC-AUC while others showed more stable relative rankings across tasks. Suggests *prior quality depends on the evaluation objective* rather than a single aggregate metric.

- ***in real data setting, random target selection = stronger performance compared to relying on predefined dataset targets***
- ***similarity patterns were more strongly aligned within prior libraries than across priors. task construction choices and implementation frameworks strongly shape task distribution***
- ***matching measured real data stats may be helpful, but not necessary.*** 

#### Bonus ablation study conducted (more relevant for us: a lever)
Conducted an ablation study on data dimensionality to examine how feature and sample configurations affect downstream performance. 

- ***increasing number of features under a fixed sample size usually led to worse performance on evaluation datasets*.** 

Possible explanation is highlighted as creating an unrealistic feature:sample ratio compared to evaluation tasks. Classification datasets had average ratio of 0.0350 and their study tested ratios of 0.05, 0.15, 0.25, and 0.02. The strongest performance naturally being 0.02.
Suggests that generation parameters shift task distribution and affect downstream performance.

#### spitballing questions that i had:
- if certain prior design choices are more suited for specific datasets, could be interesting to check performance of prior choices across domains rather than individual datasets. it might be that certain priors dominate certain domains over others. eg. maybe ticl consistently performs better on medical datasets than other priors. if this is so, why? what about its underlying distribution mimics medical datasets? maybe tabforest_forest performs better on fraud related tasks since maybe the underlying generator outputs heavily imbalanced datasets. feel like this is probably already done tho. after all we have time series specific tfms as well.


