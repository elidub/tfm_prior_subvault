modded nanotabpfns methodology:
- subsample 100 features uniformly at random
- features are fixed across folds
- tabarenas own splits are bypassed
- single random seed
- they do not resample rows across each fold like we do

nanotabpfns methodology:
- they dont subsample features. they just restrict the tabarena datasets ingested to the ones below 10 features 
- they do subsample rows to 200

turkemns evaluating data priors:
- they also dont subsample but just keep datasets upto 500 features and 5000 instances.

I disagree with using different subsample seeds to subsample different features from one tabarena dataset to create multiple datasets. treating each subset as a seperate distinct dataset assumes no correlation between the two datasets which is wrong since they inherently share the same underlying structure. Dataset A with features abc and dataset B with features def have the same underlying rows. this is gonna be an added hurdle to explain in the research paper. it would be neater to just assume a fixed subsample seed and subsample one dataset per tabarena dataset. similar to what modded nanotabpfn did. 

#### Use of tabarena metrics under current implementation
tabarenas aggregation happens over folds/repeats. in our current code implementation this is preserved since we resample after each fold/repeat. BUT keep in mind metrics like Elo are comparative to other models out there which would have been evaluated on the unmodified dataset. So we cannot use this metric.

#### evaluation
train with prior upto 5 features evaluate on 5,10,15. 
- pool of datasets gets narrower in each case. if performance degrades/improves, do we attribute this performance to our prior choice? or simply because we are evaluating on a smaller pool

 subsample all datasets upto 50 features (keep datasets with less than 50 features) and then evaluate across all. if performance improves across each combination of our feature choice then we know it has an impact. the pool size should remain fixed so as to isolate its impact.


