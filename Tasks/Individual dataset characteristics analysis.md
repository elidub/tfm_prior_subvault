---
tags:
creation date: 26-08-18
assigned:
category: after training
status: backlog
---
- Instead of using normalized scores (the aggregating TabArena metrics such as Elo, improvability, rank) which aggregate over all TabArena datasets, let's look at the performance of individual/groups of TabArena datasets and relate its performance to **characteristics of the prior** and **characteristics of the individual dataset**.
	- Do certain prior DAG configurations always work better for certain tabarena datasets?
	- For example: "Regardless of DAG type, DAGs with large edge probabilities do good on TabArena datasets with high correlations between the features", or "Regardless of edge probability and DAG type, DAGs with a high feature/nodes ratio perform better on datasets with low number of features"
	- An interesting one would also be to link this to number of features, but it's a bit less straightforward as we are controlling for it, as we subsample features.
- Similar to Figure 4.2, Appendix B en C of [[@turkmenEvaluatingDataPriors2026]]