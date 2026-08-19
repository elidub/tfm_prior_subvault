---
tags:
creation date: 26-08-11
assigned:
status: progress
category:
  - full pipeline
---

Run the entire pipeline but with _very, very_ small hparams. This includes:
- Generating many priors (DAG types, edge probabilities, number of nodes, number of features)
	- Just enough (like 2 or 3 per setting) to have variation along (almost) each axis, but few enough to keep compute-time (generating and pre-training)
- Generate very small datasets (in number of tables < 100, number of features like 2,3, also like <100 number of rows)
	- There is now a 'bug' described in [[Evaluation of number of features]] about rows <100.
- Pre-train for 2 epochs and low number of tables for very small models
	- Should take less than 5 minutes to train
- Don't have to evaluate on all TabArena datasets, but enough to have some diversity (like 5 or something?)
- Logging all results to WandB, and downloading them from there for the result analysis

The above pipeline wouldn't give any meaningful results, but it allows us to:
- Streamline the pipeline and find any bugs
- Start with an evaluation/analysis pipeline as we already have the results in the expected format. Such as
	- [[Hparam importance analysis]]
	- [[Number of feature analysis]]
	- [[Scaling law analayis]]


### Old notes
- from individual tabarena dataset performance to aggregated tabarena metrics
- during training metrics pushed to wandb , full evaluation pulls metrics from wandb and makes the plots
- could already be test for very very small hparams
	- after that can scale to the cluster

Full pipeline consists of:
1. Prior data generation
2. Training on prior
	- Evaluating on:
		- Prior data (the training)
		- TabArena light
3. Evaluation on TabArena
	- TabArena full
	- Number of feature analysis
	- Scaling law analysis
