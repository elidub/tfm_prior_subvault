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

I disagree with using different subsample seeds to subsample different features from one tabarena dataset to create multiple datasets. treating each subset as a seperate distinct dataset assumes no correlation between the two which is wrong since they share the same underlying structure. Dataset A with features abc and dataset B with features def have the same rows. this is gonna be an added hurdle to explain in the research paper. it would be neater to just assume a fixed subsample seed and subsample one dataset per tabarena dataset. similar to what modded nanotabpfn did. 

#### Use of tabarena metrics under current implementation
tabarenas aggregation happens over folds/repeats. in our current code implementation this is preserved since we resample after each fold/repeat. BUT keep in mind metrics like Elo are comparative to other models out there which would have been evaluated on the unmodified dataset. So we cannot use this metric fairly.

#### evaluation
train with prior upto 5 features evaluate on 5,10,15. 
- problem: pool of datasets gets narrower in each case. if performance degrades/improves, do we attribute this performance to our prior choice? or simply because we are evaluating on a smaller pool

proposed approach :
assume we have 3 models trained features denoted by subscript.
M_5, M_10, M_15.
evaluate these three models on datasets that have been subsampled to 5 features. this way the underlying evaluation is held constant and the only thing that we are varying is the number of features trained on. answers the question: does training the model on more features actually help? 
we could even add noise features to see whether the model is able to differentiate between informative and uninformative features.

a bonus step we could then take M_10 and M_15 and evalaute on datasets subsamples to 10 features. that way we can evaluate complexity


