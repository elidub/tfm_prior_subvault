---
tags:
creation date: 26-08-17
assigned:
  - elias
category:
  - after training
status: progress
---
- The analysis that allows [[Evaluation of number of features]] us to do.


![[Number of feature analysis 2026-08-18 09.56.35.excalidraw]]


- max feature values of $3,5,15$ are just for the sketch
- Normalized score could be any of the aggregating metrics from TabArena such as normalized ROC AUC, improvability, ELO, rank
- Keep in mind that when creating the feature subsampling, datasets with less features than the subsample feature number are skipped/dropped. So TabArena dataset collections are strictly speaking not directly comparable. This will probably doesn't have much of an impact, but might still be good to check the impact. This can be done because indiviudal dataset scores are all logged to wandb.