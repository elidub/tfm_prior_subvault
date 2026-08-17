---
tags:
creation date: 26-08-10
status: backlog
---

- A structured way to 
	- Download all TabArena datasets
	- To subsample features
	- To subsample rows (although that's probably less of a concern)

- Similar to [[@mamedovImprovingSyntheticPrior|Ali's thesis]] (but better)

When we have trained with prior up to e.g. 5 feature. We don't evaluate on all 'default' tabarena datasets, but we subsample the features (and the rows). 
- How do we subsample?
	- Do we randomly select 5 features from each dataset?
		- [ ] Do we take other subsamples per dataset repeat and/or folds?
			- If so, can we still fairly use the tabarena metrics, because I think they aggregate over repeats and/or folds.
		- [ ] Do we take more subsample seeds so we have more datasets from one tabarena dataset?
	- [ ] What do we do with datasets with less than 5 features?
		- Now the code fails, because we should make a better statistical decision for this
- We could also evaluate with more features
	- Because evaluating more datasets is not too expensive
	- E.g.: train with prior up to 5, evaluate on 5 and 10, 15 features

- The above ideas/questions could also be applied to row (subsampling) although that shouldn't be the focus on of the project.
	- Not sure if the current code now follows the tabarena repeats and therefore restirct some repeats up till 3. That's probably not needed as we subsample rows

- Checkout the code how they do it
	- [[@turkmenEvaluatingDataPriors2026]]
	- [[@pfefferleNanoTabPFNLightweightEducational2025]] (NanoTabPFN)
	- [[@ozturkSpeedrunningTabularFoundation2026]] (modded-nanoTabPFN)
- [ ] Probably best to write an 'number of feature evaluation plan' where we answer the above questions and relate it back to what the literature does.
- When doing all of this, one has to take/think about stratificaiton

- a Okay but why does TabICLv2 sample features uniformly and not log-uniformly?

#### Other code stuff
- `gtfm/gtfm/download.py:save_eval_datasets` now saved all the tabarena datasets as a single `h5` file
	- this is being saved as e.g. `eval_subsample_features = 2,3`  
		- [ ] but let's say  now we only want to evaluate the pre-training on e.g. `eval_subsample_features = 2`the code breaks. This can be optimized.