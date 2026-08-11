---
tags:
creation date: 26-08-10
status: backlog
assigned: elias
---
- A structured way to 
	- Download all TabArena datasets
	- To subsample features
	- To subsample rows (although that's probably less of a concern)

- Similar to [[@mamedovImprovingSyntheticPrior|Ali's thesis]] (but better)

When we have trained with prior up to e.g. 5 feature. We don't evaluate on all 'default' tabarena datasets, but we subsample the features (and the rows). 
- How do we subsample?
	- Do we randomly select 5 features from each dataset?
		- Do we take other subsamples per dataset repeat and/or folds?
			- If so, can we still fairly use the tabarena metrics, because I think they aggregate over repeats and/or folds.
		- Do we take more subsample seeds so we have more datasets from one tabarena dataset?
	- What do we do with datasets with less than 5 features?
- Maybe we could also evaluate with more features
	- Because evaluating more datasets is not too expensive
	- E.g.: train with prior up to 5, evaluate on 3, 5 and 10 features

The above ideas/questions could also be applied to row (subsampling) although that shouldn't be the focus on of the project.